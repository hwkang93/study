# GeoLocation API

GeoLocation API 는 사용자의 현재 위치를 가져오는 API 로 지도에 사용자 위치를 표시하는 등 다양한 용도로 사용할 수 있다.

> **https 환경이나 로컬호스트 환경에서만 사용할 수 있으며** 몇몇의 브라우저에서는 지원하지 않을 수도 있다.

다음은 내 위치의 경위도 좌표를 조회하는 예제 함수이다.

```javascript
function findMe() {
  function success(position) {
    const latitude  = position.coords.latitude;
    const longitude = position.coords.longitude;

    console.log(`my position is ${latitude} , ${longitude}`)
  }

  function error() {
    console.error('Unable to retrieve your location');
  }

  if(!navigator.geolocation) {
    console.log('Geolocation is not supported by your browser');
  } else {
    console.log('Locating…');
    navigator.geolocation.getCurrentPosition(success, error);
  }
}

findMe();
```

## Reference

https://developer.mozilla.org/ko/docs/Web/API/Geolocation_API/Using_the_Geolocation_API