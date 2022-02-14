# APFS

## APFS on iOS

- HFS+ to APFS
    - iOS 10.3부터 모든 기기가 성공적으로 APFS로 변경됨 (tvOS watchOS 등)
    - Dry Run Conversion을 사용해 성공적으로 변경함
    - Dry Run Conversion
        - file data는 안건들이고 meta data만 건들이는 법
        - 최종적으로 HFS의 Meta data만 지움
        - ![](https://i.imgur.com/68Yu9dD.png)
        - ![](https://i.imgur.com/GC6tm4u.png)
        - ![](https://i.imgur.com/66FEuak.png)
- Unicode
    - NFC vs NFD (자소가 분리되는 문제)
    - 기존에는 non-normalized UTF-8
        - 예를들어 ~n 과 같은 문자가 있을 때 이를 n + ~ 로 나눠서 저장하는 방식이었다. (iOS 11이전)
        - 보기에는 동일한 문자여도 filename이 저장되는 방식에 따라 다른 파일로 인식할 수 있었다.
    - 2 new normalized scheme
        - native normalized scheme
            - iOS11 이후로 ios에서는 erase-restores 에만 사용 지원 예정
            - mac은 case-sensitive volume 에 대해서만 사용 지원 예정
        - runtime normalized scheme
            - 현재 iOS 10.3.3 과 11에서 사용 중, 추후에 전부 native로 변경 예정
            - 그러나 이런 전환을 사용자가 직접 지웠다 깔았다 하는 것은 매우 불편한 경험이기 때문에 NFC와 NFD 사이의 전환이 자동으로 이루어지도록 새로운 runtime normalization mechanism을 도입함

## APFS on Mac

- APFS의 장점
    - cloning: fast copy
    - copy on write
        - 파일을 복사할때 레퍼런스만 복사하고 데이터의 수정이 발생하는 등 필요한 경우에만 실제 복사를 수행하는 최적화 기법
    - snapshots
        - 이걸 해서 돌아가는게 버전별 복구가 가능해짐
    - encryption
    - space sharing
    - defragmentation
- APFS 적용방법
    - 자동으로 새 OS를 깔면 System Volume은 변환될 예정
    - 그러나 Other Volume은 Manual conversion 필요
    - 적용 가능 Volume
        - HFS+
        - CoreSttorage
        - Fusion
        - FileVault
    - 간단히 volume 우클릭 후 전환하기 누르면 됨

### space sharing

> 멀티플 Volume은 space sharing이 안되었지만 APFS 에서는 가능!

![](https://i.imgur.com/SZEas9Y.png)

### Cloning, Copy on Write
    
> reference를 활용해서 시간과 공간을 절약해 복사 기능을 구현
> 만약 수정이 이루어질시 single copy해서 새로운 추가적인 reference를 가짐
    

![](https://i.imgur.com/tD3UnHc.png)


### Sparse File
    
> 기존에는 수정을 하고자 한다면 seek past the end of the file 한 후 추가로 써주는 작업이 있어서 3MB가 필요했으나 APFS부터는 실제로는 2 블록만 저장되고 읽어들였을 때에만 3블록으로 확장해서 갖는 방식
    
![](https://i.imgur.com/FhXbBlw.png)

> 놀랍게도 Snapshot을 뜨면 다음과 같이 하나의 블록이 추가되게 된다.
> 따라서 물리적 저장은 3블록을 유지한다. 만약 새로운 복사본을 너가 지우게 된다면 주황색 영역만 사라지고 여전히 APFS는 2블록이 된다.
> 요부분은 이해가 안갔음...

![](https://i.imgur.com/4eyavM4.png)
    
### Unicode
- Unicode도 HFS와 달리 조건이 다름
- 9.0를 지원함!
- Truly global
- Latest emojis
- Correctness → HFS에서는 몇몇 언어가 제대로 지원 안됐음

### Encryption
- Converter가 동작, Password and recovery key가 잘 보존됨
    
### Fusion
- All metadata is on SSD

### Defragmentation

### Snapshot
- Snapshot이 지원됨!
- Snapshot에는 모든 파일의 reference가 연결됨
- 수정이 이루어졌을 때 기존것을 남겨놓고 새로운 영역에 copy-on-write 기법을 사용해 복사
- 따라서 snapshot은 빠르게 기존 것들을 복사해 보관해 줄 수 있음
- 만약 파일을 수정하거나 삭제해도 snapshot을 기반으로 복구함

### Mobile Time Machine

맥북 백업 기능 말하는듯 (일부 삭제되버린 파일을 복구한다던지)

APFS Snapshot을 기반으로 새롭게 짰음

### 사용법 (개발자)

Filemanager에서 copyfile() 과 replace를 할때 자동으로 해준다~

따라서 Foundation API를 사용하는 것을 권장한다~
