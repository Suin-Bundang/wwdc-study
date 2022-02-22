# Dive into RealityKit 2
- RealityKit
    - AR app
    
- Entity Component System (ECS)
    ![](https://i.imgur.com/dqvcN7y.png)
    
    - Pattern for structuring data and behavior
    - Composition versus inheritance
    - Protocol 대신 Components를 사용함으로써 Entity에 언제든지 Components를 적용/삭제 할 수 있다는 장점
    - Entity
        - scene에 존재하는 하나의 thing
        - 계층적인 구조
        - Components의 container 역할
        - Components와 System으로 Behavior를 얻음
    - Components
        - System 속에서 Entity 참가자들의 마커
        - Transform과 Synchronize Components는 기본적으로 모든 Entity에 포함되어있음
        - 동적으로 Components를 추가, 삭제할 수 잇음
        - TransientComponent : entity 복사시 같이 복사되지 않는 component
        - StoreWhileEntityActive
            
            ![](https://i.imgur.com/Xi1NYMF.png)

            
        
    - System
        - System은 매 프레임마다 본인의 update 함수를 갖고 있음
        - Entity Queries
            - Scene에서 특정 Component를 가진 Entity를 찾아줌
            - System에서 사용
        - 특정 Entity를 찾으면 Motion이나 Direction등을 새롭게 바꿔주는 작용을 함 (update함수에서)
        - 이때 System의 동작 순서는 dependency로 결정 (설정을 안하면 내가 등록한 순으로 동작)
            
            ![](https://i.imgur.com/G7kr6hM.png)

            
            ![](https://i.imgur.com/UQanfFa.png)

            
- Material advancements
    - SimpleMaterial
        - baseColor
        - roughness
        - metallic
    - UnlitMaterial
        - Color
    - OcclusionMaterial
        - Masks virtual objects
    - VideoMaterial
        - Unlit
        - Video
        - Transparency
    - PhysicallyBasedMaterial
        - Simple Material의 확장판
        - 다양한 property로 설정가능
    - Custom Materials
        - Own Metal Code
- Animation advancements
    - Playback
        - Play
        - Repeat
        - Pause
        - Resume
        - Stop
        - Transition (이전 animation → 다음 animation 사이의 전환 Term)
    - FromToByAnimation
        - 여러개의 애니메이션을 USD로 불러와 사용
        - 이 때 특정 위치에서 다른 위치로 움직이고자 할 때 이동하는 위치, 중간중간 Direction을 변경할 수 있음
- Character controller
    - 캐릭터가 scene의 Collider와 물리적 상호작용하게 해주는 방법
    - LiDAR sensor를 통해 얻은 mesh of the environment와 상호작용함
    - 캐릭터와 맞는 Capsule을 만들어주기만 하면 됨
        - Height
        - Radius

