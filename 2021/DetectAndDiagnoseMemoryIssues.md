# Detect and diagnose memory issues

## impact of memory footprint

- 왜 메모리 풋프린트 신경써야하죠? → 유저 경험을 올려줍니다.
    - 메모리에 앱이 남을 확률을 올려줌 → 앱 실행을 빠르게 해줌
    - responsive experience → 유저의 반응에 즉각 대응할 수 있음 (메모리에 남아있기 때문에)
    - 많은 다른 것들을 불러올 수 잇음 (동영상 등등 메모리를 아껴서)
    - 여러 기기에 대응할 수 있음 (옛날꺼도)
    - iOS 페이지 구조
        - Dirty
            - 앱에 의해 쓰여진 메모리
            - 모든 heap Allocation
            - decoded image buffers
            - frameworks
        - Compressed
            - dirty page중 최근에 사용되지 않은 놈들
            - memory compressor 가 compress
            - 접근시 decompress
            - ios에는 swap이 없다! macOS에만 있다.
        - Clean
            - memory mapped files
            - frameworks
    - Dirty + Compressed에 대해 이야기 하겠음
        - 더 알고싶으면 WWDC2018 memory deep dive 보기~

## tools for profiling memory

- Performance XCTests
    - Memory utilization
    - CPU usage
    - Disk writes
    - Hitch rate
    - Wall clock time
    - Application launch times
    - ![](https://i.imgur.com/fbh1jz5.png)

- XCTest diagnostic
    - XCTest를 분석하고자 한다면 [enablePerformanceTestsDiagnostics](https://developer.apple.com/videos/play/wwdc2021-10180/?time=475) flag를 true로
    - Ktrace files - non memory metrics에 대해 작동
    - Memory graph - memory metrics에 대해 작동

## types of memory issues

- Leaks
    ![](https://i.imgur.com/aH07vGN.png)
    ![](https://i.imgur.com/C0Kv7es.png)
    - ARC로 많은 leaks를 해결할 수 있었음
    - 그러나 여전히 retain cycle를 조심해야한다.
    - 위에서 구한 memgraphs 파일에서 `leak의 갯수`를 확인할 수 있다!!!! Wow!!!!

- Heap size issues
    - Heap allocation regressions
        - 시작보다 더 많은 메모리를 차지하고 있는 문제
        - Remove unused allocations
        - Shrink overly large allocations
        - Deallocate memory you are finished with
        - Wait to allocate memory until you need it
        - 다음과 같은 경우에 mealData라는 변수가 Buffer처럼 데이터를 들고있어서 쓸데없이 메모리를 차지하고 있는 문제가 발생 → nil로 초기화해서 해결
        ![](https://i.imgur.com/g6JOcXb.png)
    - Fragmentation
        - 페이지는 메모리를 저장해주는 가장 작은 단위
        - 어떤 페이지에 write하게 되면 얼마를 write하던간에 전체 page가 dirty해짐
        ![](https://i.imgur.com/1MKII63.png)    
        - Allocate objects with similar lifetimes close to each other!
        - 25퍼보다 fragmentation이 낮게 유지할것!
        - autorelease pools를 사용할 것
        - 오래 작동하는 process에 대해서는 추가적인 주의를 기울일것
    - Heap size issue들은 마찬가지로 memgraph에서 확인할 수 있음
    ![](https://i.imgur.com/JYXmprC.png)
