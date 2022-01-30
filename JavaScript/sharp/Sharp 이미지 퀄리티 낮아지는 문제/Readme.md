sharp를 이용하여 이미지를 처리하다 퀄리티 이슈가 발생하였습니다.
이미지 파일의 크기를 줄이기 위해 PNG파일을 JPEG 포맷으로 변경하면서 발생하였는데, 이유와 해결 방법에 대해 알아보겠습니다.

## 왜 JPEG를 사용하면 이미지 퀄리티가 떨어질까?
[PNG](https://ko.wikipedia.org/wiki/PNG)는 [비손실 압축](https://ko.wikipedia.org/wiki/%EB%B9%84%EC%86%90%EC%8B%A4_%EC%95%95%EC%B6%95)을 이용하는 이미지 표준이며, RGB값과 함께 8비트의 알파 채널(투명도)이 있습니다. 
비손실 압축을 이용하기 때문에 이미지 손실이 없습니다. 대신, 이미지 자체의 크기를 줄이지 않는 한, 파일의 크기를 줄일 수 없기 때문에 주로 원본 이미지에 사용합니다.

[JPEG](https://ko.wikipedia.org/wiki/JPEG)는 [손실 압축](https://ko.wikipedia.org/wiki/%EC%86%90%EC%8B%A4_%EC%95%95%EC%B6%95)을 이용한 이미지 표준 중 하나이며, PNG와 다르게 알파채널이 존재하지 않습니다. 
손실 압축을 이용하기 때문에 이미지를 저장할 때마다 이미지 품질이 감소할 수 밖에 없습니다.
그래서 이미지를 계속 저장하다보면 손실이 누적되어 흐려지게 됩니다. (디지털 풍화)

하지만 이미지 크기를 효과적으로 줄일 수 있기 때문에 웹 상에서는 JPEG를 많이 사용합니다.
하지만, 격자무늬나 문자, 선 등 고주파 성분이 많은 이미지의 변환에는 나쁜 품질을 보이는 경우가 많습니다.


## sharp에서 JPEG 이미지 퀄리티 보존하기
Sharp.js를 이용하여 퀄리티를 보존하면서 JPEG파일로 변환하려면 아래와 같은 옵션을 사용해야 합니다.

```javascript
const jpegImage = sharp(pngImage)
        .jpeg({quality: 100, chromaSubsampling:"4:4:4"})
        .toBuffer();
```

`quality`옵션은 말 그대로 품질에 대한 옵션입니다.
이는 0-100까지의 범위를 가지며 기본적으로 80%의 퀄리티를 사용합니다.
100%가 아닌 퀄리티로 변환을 계속하면 이미지 품질이 계속 떨어지기 때문에 주의해야 합니다.

`chromaSubsampling`옵션은 기본적으로 4:2:2로 설정되어있습니다.
4:4:4는 Chroma Subsampling을 수행하지 않는 것을 의미합니다. 즉, 서브샘플링을 하지 않습니다.

Chroma Subsampling을 간단히 설명하면, 사람은 밝기의 변화보다 색상의 변화에 둔감하다는 점을 이용하여  시각적 손실을 최소화하면서 데이터의 용량을 절약하는 기술입니다.
Chroma Subsampling에 대한 자세한 내용은 [위키](https://ko.wikipedia.org/wiki/%ED%81%AC%EB%A1%9C%EB%A7%88_%EC%84%9C%EB%B8%8C%EC%83%98%ED%94%8C%EB%A7%81)를 참고하세요.


## Reference
[JPEG - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/JPEG)
[손실 압축 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EC%86%90%EC%8B%A4_%EC%95%95%EC%B6%95)
[크로마 서브샘플링 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%ED%81%AC%EB%A1%9C%EB%A7%88_%EC%84%9C%EB%B8%8C%EC%83%98%ED%94%8C%EB%A7%81)
[PNG - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/PNG)
[비손실 압축 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EB%B9%84%EC%86%90%EC%8B%A4_%EC%95%95%EC%B6%95)
[sharp - High performance Node.js image processing](https://sharp.pixelplumbing.com/api-output#jpeg)