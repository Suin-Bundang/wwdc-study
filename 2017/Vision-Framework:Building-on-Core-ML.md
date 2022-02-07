# Vision으로 할 수 있는 것

## 종류
- Face Detection
    - 옆모습이나 가려진 얼굴도 인식 가능
    - Face Landmarks (눈썹/눈/코/입 등으로 구분해 인식가능)
- Image Registration 
    - 여러 이미지를 이용해 Panorama 사진 만들기
- Rectangle Detection
- Barcode Detection
- Text Detection
- Object Tracking

## 특징
- On-Device
    - Privacy
    - Low Cost
    - Real-Time

# Vision 개념

- 이미지 분석하기
    - Request를 RequestHandler에 넣으면 Observation을 반환함
    - ![이미지분석과정1](https://i.imgur.com/ljx7fg1.png)
    - ![이미지분석과정2](https://i.imgur.com/PbLDHIx.png)
    - 한 이미지 안에 다양한 요청을 동시에 처리할 수 있음
- 트래킹하기
    - Request를 SequenceRequestHandler에 넣으면 Observation을 반환함
    - SequenceRequestHandler는 여러 요청을 동시에 처리 불가능
    - ![트래킹과정](https://i.imgur.com/txS5WSC.png)

- 사용 가능 이미지 타입
    - CVPixelBufferRef
    - CGImageRef
        - 이미지가 이미 UI상에 사용될때
    - CIImage
    - NSURL
        - 디스크의 이미지 파일일때
    - NSData
        - 네트워크 이미지일때

# 코드 예시

- 얼굴 인식
``` swift
// Create request
let faceDetectionRequest = VNDetectFaceRectanglesRequest()

// Create request handler
let myRequestHandler = (url: fileURL, options: [:])

// send the requests to the request handler
myRequestHandler.perform([faceDetectionRequest])

// Do we have a face
for observation in faceDetectionRequest.results as! [VNFaceObservation] {
    // do something
}
```

- 오브젝트 트래킹
``` swift
// Create a sequence request handler
let requestHandler = VNSequenceRequestHandler()

// Start the tracking with an observation
let observations = detectionRequest.results as! [VNDetectedObjectObservation]
let objectsToTrack = observations.map { VNTrackObjectRequest(detectedObjectObservation: $0) }

// Run the requests
requestHandler.perform(objectsToTrack, on: pixelBuffer)

// Lets look at the results
for request in objectsToTrack {
    for observation in request.results as! [VNDetectedObjectObservation] {
        // do something
    }
}
    
```

## Best Practices

- 어떤 이미지 타입을 사용할지
    - 위에 적어놓음
- 이미지로 무슨 작업을 할지
    - 탐색을 할 것인지, 트래킹을 할 것인지 선택
- 어느정도의 성능이 요구되는지
    - QOS를 적절히 조절

## Face Detector Landscape
- 얼굴 인식 기능은 Vision뿐만 아니라 다른 프레임워크도 제공해줌
- 각각의 장단점이 있으므로 비교 후 사용
- ![랜드스케이프](https://i.imgur.com/lyPyP0A.png)

# 요약

애플이 컴퓨터 비전 모르는 사람들 위해서 비전 프레임워크를 쉽게 만들었다.
알아서 갖다 써라. 근데 필요한거 없으면 coreML로 직접 모델 만들어서 써라.
