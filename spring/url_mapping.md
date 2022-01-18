# Controller URL 매핑 할 때...

예전에 받았던 피드백인데, 기록해놓으면 좋을 것 같다.

```java
@RestController
@RequestMapping("/location")
@RequiredArgsConstructor
public class LocationController {

    private final LocationService locationService;

    @GetMapping("/latlng")
    public ResponseEntity<LocationDto> findByLatLng(LatLng latLng) {
        return locationService.findByLatLng(latLng);
    }

    @GetMapping("/address")
    public ResponseEntity<LocationDto> findByAddress(String address) {
        return locationService.findByAddress(address);
    }

    @GetMapping("/pnu")
    public ResponseEntity<LocationDto> findByPnu(String pnu) {
        return locationService.findByPnu(pnu);
    }
}
```

이런 비스무리한 소스코드의 위치 찾기 기능을 가진 API 컨트롤러였는데 ```@RequestMapping("/location")``` 를 없애고 
```@GetMapping``` 에 URL 전체를 명시하는게 어떻냐는 것이었다.

이유는 유지보수의 편의를 위해서였다.

위의 소스코드는 URL 요청에서 오류가 발생하는 경우 소스코드를 찾기 힘들다는 문제를 가지고 있다.
예를 들어 ```/location/latlng``` 라는 URL 을 호출할 때 오류가 발생했다고 가정해보자.

남이 작성했기 때문에 소스코드에 익숙지 않을 담당자는 당연히 찾고자 하는 URL 이 어느 컨트롤러에 있는지 모른다.

그럼 IDE 에서 URL 을 검색해 오류 메소드를 찾아야 하는데, ```/location/latlng``` 로 검색한다고 해서 찾고자하는 컨트롤러를 찾을 수 없다.
URL 을 분리해놓으면 검색한다고 해도 원하는 결과가 나오지 않을 것이며, 결국 담당자는 URL 의 일부분만으로 검색을 해야 한다.

그리고 그 일부분이 다른 URL 또는 메소드에서 사용하고 있을 경우 검색하는 데 더 많은 시간이 소요된다.

API 는 직관적이고 명확한 URL 으로 만들어져야 한다. 그렇기 때문에 비교적 쉬운 명사들을 많이 사용하는 편이고,
구현 중인 API 의 특성상(MAP 관련 API) location, latlng 라는 단어는 여러 곳에서 사용하고 있을 가능성이 높다.
결국 location, latlng 로 검색할 경우 원하지 않은 결과도 많이 나올 테고, 찾아야 할 소스코드를 찾는데 시간이 오래 소요될 수밖에 없다.

나름 예쁘게(?) 소스코드를 작성하려고 일부러 한 건데 유지보수를 하시는 분들 입장에서는 충분히 어려움을 겪을 수 있다고 느꼈고,
그 일을 계기로 조금 더 예쁜 소스코드를 작성할 수 있게 되었다.


























