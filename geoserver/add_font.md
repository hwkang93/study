# GeoServer 에 폰트 추가하는 방법

지오서버는 지오서버가 설치된 OS 내에 있는 폰트를 사용한다.
그렇기 때문에 지오서버에 폰트를 추가하기 위해서는, OS 에 폰트를 설치해야 한다.

다음 API 요청을 통해 지오서버에서 사용할 수 있는 폰트의 목록을 조회할 수 있다.

```
http://localhost:5432/geoserver/rest/fonts
```

다음은 Window , CentOS 의 폰트 디렉토리이다.

#### Window

```
C:\Windows\Fonts
```

#### CentOS

```bash
/usr/share/fonts
```

> CentOS 에서는 폰트 설치 후 추가로 캐시 삭제를 해줘야한다. (fc-cache -f -v)


각 OS 에서 폰트를 추가한 후 지오서버를 재기동하면 폰트가 추가됐음을 확인할 수 있다.