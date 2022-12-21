## Container > NHN Container Service(NCS) > 개요

NHN Container Service(NCS)는 컨테이너를 구동하는 환경을 제공하는 서비스입니다.
VM 인스턴스, Kubernetes와 같은 컨테이너 실행 환경을 구성하지 않아도 이 서비스를 이용하여 컨테이너를 실행할 수 있습니다.

## 특징

* 퍼블릭/프라이빗 컨테이너 레지스트리에 보관된 컨테이너 이미지를 구동시킬 수 있습니다.
* 컨테이너는 사용자 VPC에 연결되므로 VPC를 통해 통신 가능한 인스턴스, 로드 밸런서, Online NAS 등 모든 IaaS 자원과 통신할 수 있고, VPC가 제공하는 네트워킹 기능을 활용할 수 있습니다.
* 컨테이너는 Online NAS 또는 VPC에서 접근 가능한 NFSv3 마운트 포인트를 볼륨으로 사용할 수 있습니다.
* GPU를 사용하는 컨테이너를 구성할 수 있습니다.
* 컨테이너에 로드 밸런서를 연결할 수 있습니다. 생성된 플로팅 IP와 도메인을 통해 컨테이너 서비스를 외부에 노출시킬 수 있습니다.

## 구성 및 용어

다음은 NCS 서비스 구성 예시입니다.

![NCS_overview_01.png](https://static.toastoven.net/prod_ncs/20221215/NCS_overview_01.png)

### 템플릿

어떤 사양의 컨테이너를 구성할 것인지 `템플릿`에 정의합니다.
`템플릿`에는 템플릿 이름, 설명, 연결할 VPC, 한 개 이상의 컨테이너 사양을 기술합니다.
컨테이너 사양에는 구동시킬 컨테이너 이미지가 보관된 레지스트리 URL, 사용할 CPU, GPU, Memory 등 자원 사양, 컨테이너가 사용하는 포트, 연결할 NAS 스토리지 등을 기술합니다.

```
템플릿을 생성해도 컨테이너는 생성되지 않습니다. 템플릿은 워크로드를 만들기 위한 틀이며, 템플릿을 활용해 워크로드를 생성한 경우에 컨테이너가 생성됩니다.

템플릿을 유지한 상태로 필요할 때만 워크로드를 생성하여 컨테이너를 구동할 수도 있습니다.
```

### 워크로드

컨테이너 사양이 기술된 `템플릿`을 참조하여 `워크로드`를 정의합니다.
`워크로드`에는 참조되는 `템플릿`, 워크로드 실행 수, 로드밸런서 사용 여부, 플로팅 IP 사용 여부를 기술합니다.
위 그림은 `템플릿`에 2개의 컨테이너 사양을 정의한 상태에서 워크로드 실행 수를 2로 설정하여 `워크로드`를 생성한 경우 어떻게 구성되는지 보여줍니다.

워크로드 생성 시 템플릿에 정의한 컨테이너가 `작업` 하위에 생성됩니다.
`작업`에는 VPC 서브넷으로 부터 할당 받은 IP가 지정됩니다.
VPC에 접근 가능한 곳에서 생성된 컨테이너 포트로 접근하려면 `작업IP:컨테이너포트`로 접근할 수 있습니다.

워크로드 실행 수에 지정한 갯수 만큼 `작업`이 실행됩니다. 위 예에서는 2로 지정하였기 때문에 두 개의 작업이 생성되었습니다.

### 로드 밸런서

워크로드에 컨테이너 실행 수를 여러 개로 설정하고 로드 밸런서를 연결하면 트래픽 부하를 개별 컨테이너로 분배할 수 있습니다.
플로팅 IP 사용하도록 설정한 경우 로드 밸런서에 FIP가 할당되고 도메인도 생성됩니다.

```
지정한 VPC 서브넷에 인터넷 게이트웨이가 연결되어 있지 않은 경우 플로팅 IP와 도메인을 사용할 수 없습니다.
```