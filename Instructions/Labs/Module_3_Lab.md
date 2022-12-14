---
lab:
    title: '3: Azure Migrate를 사용하여 Hyper-V VM을 Azure로 마이그레이션'
    module: '모듈 3: 마이그레이션을 위한 설계'
---

# 랩: Azure Migrate를 사용하여 Hyper-V VM을 Azure로 마이그레이션
# 학생 랩 매뉴얼

## 랩 시나리오

Adatum 엔터프라이즈 아키텍처 팀은 Azure로의 마이그레이션의 일환으로 워크로드를 현대화하려는 포부에도 불구하고 공격적인 일정으로 인해 많은 경우 리프트 앤 시프트 접근 방식을 따라야 한다는 것을 깨닫습니다. 이 작업을 단순화하기 위해 Adatum 엔터프라이즈 아키텍처 팀은 Azure Migrate의 기능을 탐색하기 시작했습니다. Azure Migrate는 Azure 온-프레미스 서버, 인프라, 애플리케이션 및 데이터로 평가하고 마이그레이션하는 중앙 집중식 허브 역할을 합니다.

Azure Migrate는 다음과 같은 기능을 제공합니다.

- 통합 마이그레이션 플랫폼: 하나의 포털에서 Azure로의 마이그레이션을 시작, 실행, 추적할 수 있습니다.
- 광범위한 도구: 다양한 평가 및 마이그레이션 도구를 사용할 수 있습니다. 도구에는 Azure Migrate: 서버 평가 및 Azure Migrate: 서버 마이그레이션이 포함됩니다. Azure Migrate는 다른 Azure 서비스, 기타 도구 및 ISV(Independent Software Vendor) 오퍼링과 통합됩니다.
- 평가 및 마이그레이션: Azure Migrate 허브에서 다음을 평가하고 마이그레이션할 수 있습니다.
- Servers: 온-프레미스 서버를 평가하고 Azure 가상 머신으로 마이그레이션합니다.
- 데이터베이스: 온-프레미스 데이터베이스를 평가하고 Azure SQL Database 또는 SQL Managed Instance로 마이그레이션합니다.
- 웹 애플리케이션: 온-프레미스 웹 애플리케이션을 평가하고 Azure App Service 마이그레이션 도우미를 사용하여 Azure App Service로 마이그레이션합니다.
- 가상 데스크톱: 온-프레미스 VDI(가상 데스크톱 인프라)를 평가하고 Azure의 Windows Virtual Desktop으로 마이그레이션합니다.
- 데이터: Azure Data Box 제품을 사용하여 대량의 데이터를 Azure로 빠르고 비용 효율적으로 마이그레이션합니다.

데이터베이스, 웹앱 및 가상 데스크톱이 마이그레이션 이니셔티브의 다음 단계의 범위에 있는 동안, Adatum 엔터프라이즈 아키텍처 팀은 온-프레미스 Hyper-V 가상 머신을 Azure VM으로 마이그레이션하기 위해 Azure Migrate 사용을 평가하여 시작하려고 합니다.

## 목표
  
이 랩을 완료하면 다음과 같은 작업을 수행할 수 있습니다.

-  Azure Migrate를 사용하여 평가 및 마이그레이션할 Hyper-V 준비

-  Azure Migrate를 사용하여 마이그레이션할 Hyper-V 평가

-  Azure Migrate를 사용하여 Hyper-V VM 마이그레이션


## 랩 환경
  
Windows 서버 관리자 자격 증명

-  사용자 이름: **Student**

-  암호: **Pa55w.rd1234**

예상 소요 시간: 120분


## Lab Files

-  \\\\AZ304\\AllFiles\\Labs\\08\\azuredeploy30308suba.json


### 연습 0: 랩 환경 준비

이 연습의 주요 작업은 다음과 같습니다.

1. Azure Resource Manager 빠른 시작 템플릿을 사용하여 Azure VM 배포.

1. Azure VM에서 중첩된 가상화 구성


#### 작업 1: Azure Resource Manager 빠른 시작 템플릿을 사용하여 Azure VM 배포.

1. 랩 컴퓨터에서 웹 브라우저를 시작하여 [Azure Portal](https://portal.azure.com) - `portal.azure.com`으로 이동한 후 이 랩에서 사용할 구독의 Owner 역할인 사용자 계정 자격 증명을 입력하여 로그인합니다.

1. Azure Portal에서 검색 텍스트 상자의 오른쪽에 있는 도구 모음 아이콘을 직접 선택하여 **Cloud Shell** 창을 엽니다.

1. **Bash** 또는 **PowerShell**을 선택하라는 메시지가 표시되면 **PowerShell**을 선택합니다. 

   >**참고**: **Cloud Shell**을 처음 시작하고 **탑재된 스토리지가 없음** 메시지를 받으면, 이 랩에서 사용하는 구독을 선택하고 **스토리지 만들기**를 선택합니다. 

1. Cloud Shell 창의 도구 모음에서 **파일 업로드/다운로드** 아이콘을 선택한 다음, 드롭다운 메뉴에서 **업로드**를 선택하여 **\\\\AZ303\\AllFiles\Labs\\08\\azuredeploy30308suba.json** 파일을 Cloud Shell 홈 디렉터리에 업로드합니다.

1. Cloud Shell에서 다음 명령을 실행하여 현재 위치에서 가까운 Azure 지역으로 지정된 location 변수를 설정합니다. '<Azure region>' 자리 표시자는 구독에서 Azure VM 배포용으로 사용 가능하며 랩 컴퓨터 위치와 가장 가까운 Azure 지역 이름(예: 'eastus')으로 바꿉니다.

   ```powershell
   $location = '<Azure region>'
   ```

      > **참고**: Azure VM을 프로비전할 수 있는 Azure 지역을 확인하려면 [**https://azure.microsoft.com/ko-kr/regions/offers/**](https://azure.microsoft.com/ko-kr/regions/offers/)을 참조하세요.
      
1. Cloud Shell 창에서 다음 명령을 실행하여 리소스 그룹을 만듭니다.

   ```powershell
   New-AzSubscriptionDeployment `
     -Location $location `
     -Name az30308subaDeployment `
     -TemplateFile $HOME/azuredeploy30308suba.json `
     -rgLocation $location `
     -rgName 'az30308a-labRG'
   ```

1. Azure Portal에서 **Cloud Shell** 창을 닫습니다.

1. 랩 컴퓨터에서 다른 브라우저 탭을 열고 [301-nested-vms-in-virtual-network Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/301-nested-vms-in-virtual-network)으로 이동한 다음 **Azure에 배포**를 선택합니다. 이렇게 하면 브라우저를 자동으로 Azure Portal에 있는 **중첩된 VM이 있는 Hyper-V 호스트 가상 머신** 블레이드로 리디렉션합니다.

    ``` url
    https://github.com/Azure/azure-quickstart-templates/tree/master/301-nested-vms-in-virtual-network
    ```

1. Azure Portal의 **중첩된 VM이 있는 Hyper-V 호스트 가상 머신** 블레이드에서 다음 설정을 지정합니다(다른 설정을 기본값으로 남겨둡니다).

    | 설정 | 값 | 
    | --- | --- |
    | 구독 | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 리소스 그룹 | **az30308a-LabRG** |
    | 호스트 공용 IP 주소 이름 | **az30308a-hv-vm-pip** |
    | Virtual Network 이름 | **az30308a-hv-vnet** |
    | 호스트 네트워크 인터페이스1이름 | **az30308a-hv-vm-nic1** |
    | 호스트 네트워크 인터페이스2이름 | **az30308a-hv-vm-nic2** |
    | 호스트 가상 머신 이름 | **az30308a-hv-vm** |
    | 호스트 관리자 사용자 이름 | **학생** |
    | 호스트 관리자 암호 | **Pa55w.rd1234** |

1. **중첩된 VM이 포함된 Hyper-V 호스트 가상 머신** 블레이드에서 **검토 + 만들기**를 선택한 다음 **만들기**를 선택합니다.

    > **참고**: 배포가 완료될 때까지 기다립니다. 배포에는 약 10분이 소요됩니다.

#### 작업 2: Azure VM에서 중첩된 가상화 구성

1. Azure Portal에서 **가상 머신**을 검색 및 선택하고 **가상 머신** 블레이드에서 **az30308a-hv-vm**을 선택합니다.

1. **az30308a-hv-vm** 블레이드에서 **네트워킹**을 선택합니다. 

1. **az30308a-hv-vm** 에서 **| 네트워킹** 블레이드에서 **az30308a-hv-vm-nic1** 탭이 선택되어 있는지 확인한 다음 **인바운드 포트 규칙 추가**를 선택합니다.

    >**참고**: 공용 IP 주소가 할당된 **az30308a-hv-vm-nic1**의 설정을 수정해야 합니다.

1. **인바운드 보안 규칙 추가** 블레이드에서 다음 설정을 지정하고(다른 설정은 기본값으로 남겨둡니다) **추가**를 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 대상 포트 범위 | **3389** |
    | 프로토콜 | **모두** |
    | 이름 | **AllowRDPInBound** |

1. **az30308a-hv-vm** 블레이드에서 **개요**를 선택합니다. 

1. **az30308a-hv-vm** 블레이드의 드롭다운 메뉴에서 **연결**을 선택하고 **RDP**를 선택한 다음 **RDP 파일 다운로드**를 클릭합니다.

1. 메시지가 표시되면 **연결**을 클릭하고 다음 자격 증명으로 로그인합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 사용자 이름 | **학생** |
    | 암호 | **Pa55w.rd1234** |

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내 서버 관리자 창에서 **로컬 서버**를 클릭하고 **IE 강화된 보안 구성** 레이블 옆에 있는 **켜기** 링크를 클릭한 뒤 **IE 강화된 보안 구성** 대화 상자에서 둘 다 **끄기** 옵션을 선택하고 **확인**을 클릭합니다.

1. 원격 데스크톱 세션에서 파일 탐색기를 열고 **F:** 드라이브로 이동합니다. **VHDs** 폴더를 만듭니다.

1. **az30308a-hv-vm-nic1**에 대한 원격 데스크톱 세션 내에서 Internet Explorer를 시작하여 [Microsoft Edge](https://www.microsoft.com/ko-kr/edge/business/download) 다운로드 페이지로 이동하고 Microsoft Edge 설치 프로그램을 다운로드하여 설치를 실행합니다. 

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 Microsoft Edge를 통해 [Windows Server 평가판](https://www.microsoft.com/ko-kr/evalcenter/evaluate-windows-server-2019) 페이지로 이동한 다음 Windows Server 2019 **VHD** 파일을 **F:\VHDs** 폴더에 다운로드합니다. 
    
    ```url
    https://www.microsoft.com/ko-kr/evalcenter/evaluate-windows-server-2019
    ```
    
    > **참고**: 평가판 페이지에서 다운로드를 완료하려면 개인 정보를 입력하라는 메시지가 표시됩니다. '영국'이나 기타 국가를 선택하면 알림을 수신 거부할 수 있습니다.

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 시작, **Windows 관리 도구**를 차례로 클릭하고 **Hyper-V 관리자**를 시작합니다. 

1. **Hyper-V 관리자** 콘솔에서 **az30308a-hv-vm** 노드를 선택합니다. 

1. **새로 만들기**를 클릭하고 계단식 메뉴에서 **가상 머신**을 선택합니다. 이렇게 하면 **새 가상 머신 마법사**가 시작됩니다. 

1. **새 가상 머신 마법사**의 **시작하기 전** 페이지에서 **다음 >** 을 클릭합니다.

1. **새 가상 머신 마법사**의 **이름 및 위치 지정** 페이지에서 다음 설정을 지정하고 **다음 >** 을 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 이름 | **az30308a-vm1** | 
    | 가상 머신을 다른 위치에 저장 | 선택 | 
    | 위치 | **F:\VMs** |

    >**참고**: **F:\VMs** 폴더를 만들어야 합니다.

1. **새 가상 머신 마법사**의 **생성 지정** 페이지에서 **생성 1** 옵션을 선택하고 **다음 >** 을 선택합니다.

1. **새 가상 머신 마법사**의 **메모리 할당** 페이지에서 **시작 메모리**를 **2048**로 설정하고 **다음 >** 을 선택합니다.

1. **새 가상 머신 마법사**의 **네트워킹 구성** 페이지에 있는 **연결** 드롭다운 리스트에서 **NestedSwitch**를 선택하고 **다음 >** 을 선택합니다.

1. **새 가상 머신 마법사**의 **가상 하드 디스크 연결** 페이지에서 **기존 가상 하드 디스크 사용** 옵션을 선택하여 다운로드한 VHD 파일을 **F:\VHD** 폴더로 위치를 설정하고 **다음 >** 을 선택합니다.

1. **새 가상 머신 마법사**의 **요약** 페이지에서 **완료**를 선택합니다.

1. **Hyper-V 관리자** 콘솔에서 새로 만든 가상 머신을 선택하고 **시작**을 선택합니다. 

1. **Hyper-V 관리자** 콘솔에서 가상 머신이 실행 중인지 확인하고 **연결**을 선택합니다. 

1. **az30308a-vm1**에 대한 가상 머신 연결 창의 **Hi there** 페이지에서 **다음**을 선택합니다. 

1. **az30308a-vm1**에 대한 가상 머신 연결 창의 **사용 조건** 페이지에서 **수락**을 선택합니다. 

1. **az30308a-vm1**에 대한 가상 머신 연결 창의 **사용자 지정 설정** 페이지에서 기본 제공 관리자 계정의 암호를 **Pa55w.rd1234**로 설정하고 **마침**을 선택합니다. 

1. **az30308a-vm1**에 대한 가상 머신 연결 창에서 새로 설정된 암호를 사용하여 로그인합니다.

1. **az30308a-vm1**에 대한 가상 머신 연결 창에서 Windows PowerShell을 시작하고 **관리자**에서 다음을 수행합니다.**Windows PowerShell** 창은 다음을 실행하여 컴퓨터 이름을 설정합니다. 

   ```powershell
   Rename-Computer -NewName 'az30308a-vm1' -Restart
   ```

### 연습 1: Azure Migrate를 사용하여 평가 및 마이그레이션 준비
  
이 연습의 주요 작업은 다음과 같습니다.

1. Hyper-V 환경 구성

1. Azure Migrate 프로젝트 만들기

1. 대상 Azure 환경 구현


#### 작업 1: Hyper-V 환경 구성

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 Microsoft Edge를 시작하고 [Microsoft 다운로드 센터](https://aka.ms/migrate/script/hyperv)로 이동한 후 **F:** 드라이브에 구성 PowerShell 스크립트를 다운로드합니다.

    ```url
    https://aka.ms/migrate/script/hyperv
    ```

    >**참고**: 이 스크립트는 다음 작업을 수행합니다.

    - 지원되는 PowerShell 버전에서 스크립트를 실행 중임을 확인합니다.

    - Hyper-V 호스트에 관리 권한이 있는지 확인합니다.

    - Azure Migrate 서비스가 Hyper-V 호스트와 통신하는 데 사용하는 로컬 사용자 계정을 만들 수 있습니다. 이 사용자 계정은 Hyper-V 호스트의 원격 관리 사용자, Hyper-V 관리자 및 성능 모니터 사용자 그룹에 추가됩니다.

    - 호스트가 지원되는 버전의 Hyper-V 및 Hyper-V 역할을 실행하고 있는지 확인합니다.

    - WinRM 서비스를 활성화하고 호스트에서 포트 5985(HTTP) 및 5986(HTTPS)을 엽니다. 이는 메타데이터 수집에 필요합니다.

    - 호스트에 대한 PowerShell 원격 기능을 활성화합니다.

    - 호스트가 관리하는 모든 VM에서 Hyper-V 통합 서비스가 활성화되어 있는지 확인합니다.

    - 필요한 경우 호스트에서 CredSSP를 사용할 수 있습니다.

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 **Windows PowerShell ISE**를 시작합니다. 

1. **관리자: Windows PowerShell ISE** 창의 콘솔 창에서 다음을 실행하여 Zone.Identifier 대체 데이터 스트림을 제거합니다. 이 경우, 파일이 인터넷에서 다운로드했음을 나타냅니다.

   ```powershell
   Unblock-File -Path F:\MicrosoftAzureMigrate-Hyper-V.ps1
   ```

1. **관리자: Windows PowerShell ISE** 창에서 **F:** 폴더에 있는 **MicrosoftAzureMigrate-Hyper-V.ps1** 스크립트를 열어서 실행합니다. 확인 메시지가 표시되면 다음 프롬프트를 제외하고(이 경우, **N** 키를 누르고 **Enter** 키를 누름) **Y** 키를 누른 후 **Enter** 키를 누릅니다.

- SMB 공유를 사용하여 VHD를 저장하시겠습니까?

- Azure Migrate 및 Hyper-V 호스트 통신에 대한 비관리자 로컬 사용자를 만드시겠습니까? 


#### 작업 2: Azure Migrate 프로젝트 만들기

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 Microsoft Edge를 시작하고 [Azure Portal](https://portal.azure.com)로 이동하며 사용자 계정의 자격 증명을 이 랩에서 사용할 구독에서 Owner 역할로 제공하여 로그인합니다.

1. Azure Portal에서 **Azure Migrate**를 검색하여 선택하고 **Azure Migrate** 블레이드의 **마이그레이션 목표** 섹션에서 **서버**를 선택한 후 **프로젝트 만들기**를 선택합니다.

1. **새 컨테이너** 블레이드에서 다음 설정을 지정하고(기타 설정은 기본값 유지) **만들기**를 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 구독 | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 리소스 그룹 | 새 리소스 그룹 **az30308b-labRG**의 이름 |
    | 프로젝트 마이그레이션 | **az30308b-migrate-project** |
    | 지리 | 해당 국가 또는 지리적 영역의 이름 |


#### 작업 3: 대상 Azure 환경 구현

1. Azure Portal에서 **가상 네트워크**를 검색 및 선택하고 **가상 네트워크** 블레이드에서 **+ 신규**를 선택합니다.

1. **가상 네트워크 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고(나머지는 기본값을 그대로 유지) **다음:**을 선택합니다.** IP 주소**:

    | 설정 | 값 |
    | --- | --- |
    | 구독 | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 리소스 그룹 | 새 리소스 그룹 **az30308c-labRG**의 이름 |
    | 이름 | **az30308c-migration-vnet** |
    | 지역 | 이 랩의 앞부분에서 가상 머신을 배포한 Azure 지역의 이름 |

1. **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에서 **IPv4 주소 공간** 입력란에 **10.8.0.0/16**을 입력하고 **+ 서브넷 추가**를 선택합니다.

1. **서브넷 추가** 블레이드에서 다음 설정(다른 설정은 기본값을 그대로 사용)을 지정하고 **추가**를 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | 서브넷 이름 | **subnet0** |
    | 서브넷 주소 범위 | **10.8.0.0/24** |

1. **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에 돌아와서 **검토 + 만들기**를 선택합니다.

1. **가상 네트워크 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 선택합니다.

1. Azure Portal에서 **가상 네트워크**를 검색 및 선택하고 **가상 네트워크** 블레이드에서 **+ 신규**를 선택합니다.

1. **가상 네트워크 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정하고(나머지는 기본값을 그대로 유지) **다음:**을 선택합니다.** IP 주소**:

    | 설정 | 값 |
    | --- | --- |
    | 구독 | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 리소스 그룹 | **az30308c-LabRG** |
    | 이름 | **az30308c-test-vnet** |
    | 지역 | 이 랩의 앞부분에서 가상 머신을 배포한 Azure 지역의 이름 |

1. **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에서 **IPv4 주소 공간** 입력란에 **10.8.0.0/16**을 입력하고 **+ 서브넷 추가**를 선택합니다.

1. **서브넷 추가** 블레이드에서 다음 설정(다른 설정은 기본값을 그대로 사용)을 지정하고 **추가**를 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | 서브넷 이름 | **subnet0** |
    | 서브넷 주소 범위 | **10.8.0.0/24** |

1. **가상 네트워크 만들기** 블레이드의 **IP 주소** 탭에 돌아와서 **검토 + 만들기**를 선택합니다.

1. **가상 네트워크 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 선택합니다.

1. Azure Portal에서 **스토리지 계정**을 검색 및 선택하고 **스토리지 계정** 블레이드에서 **+ 신규**를 선택합니다.

1. **스토리지 계정 만들기** 블레이드의 **기본** 탭에서 다음 설정을 지정합니다(다른 설정은 기본값으로 유지).

    | 설정 | 값 | 
    | --- | --- |
    | 구독 | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 리소스 그룹 | **az30308c-LabRG** |
    | 스토리지 계정 이름 | 문자와 숫자로 구성된 3~24자 사이의 전역적으로 고유한 이름 |
    | 위치 | 본 작업의 앞부분에서 가상 네트워크를 만든 곳인 Azure 지역의 이름 |
    | 성능 | **표준** |
    | 계정 종류 | **StorageV2(범용 v2)** |
    | 복제 | **LRS(로컬 중복 스토리지)** |

1. **스토리지 계정 만들기** 블레이드의 **기본** 탭에서 **검토 + 만들기**를 선택합니다.

1. **스토리지 계정 만들기** 블레이드의 **검토 + 만들기** 탭에서 **만들기**를 선택합니다.


### 연습 2: Azure Migrate를 사용하여 마이그레이션할 Hyper-V 평가
  
이 연습의 주요 작업은 다음과 같습니다.

1. Azure Migrate 어플라이언스 배포 및 구성

1. 평가 구성, 실행 및 보기


#### 작업 1: Azure Migrate 어플라이언스 배포 및 구성

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 Microsoft Edge 창을 통해 Azure Portal로 이동한 후 **Azure Migrate**를 검색하여 선택합니다.

1. **Azure Migrate | 서버** 블레이드에서 **검색**(**Azure Migrate: 서버 평가** 타일에 있음)을 선택합니다. 

1. **컴퓨터 검색** 블레이드의 **컴퓨터가 가상화되어 있습니까?** 드롭다운 목록에서 **예, Hyper-V 사용**을 선택합니다. 

1. **컴퓨터 검색** 블레이드의 **어플라이언스 이름 지정** 텍스트 상자에 **az30308a-vma1**을 입력하고 **키 생성** 단추를 선택합니다.

   >**참고**: Azure Migrate 프로젝트 키 생성 중에 권한 관련 오류가 발생하면 Azure Portal에서 **구독** 블레이드로 이동하여 구독을 선택합니다. 그런 다음 구독 블레이드에서 **액세스 제어(IAM)** 를 선택하고 Azure AD 사용자 계정에 **Owner** 역할을 할당합니다.

1. 리소스 프로비전이 완료될 때까지 기다린 후 **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 메모장을 시작하고 **Azure Migrate 프로젝트 키**를 메모장에 복사합니다. 

1. **컴퓨터 검색** 블레이드의 **Azure Migrate 어플라이언스 다운로드** 텍스트 상자에서 **VHD 파일 옵션**, **다운로드**를 차례로 선택하고 메시지가 표시되면 다운로드 위치를 **F:\VMs** 폴더로 설정합니다.

   >**참고**: 다운로드가 완료될 때까지 기다립니다. 완료되려면 약 5분이 소요됩니다.

1. 다운로드가 완료되면 다운로드한 .ZIP 파일의 콘텐츠를 **F:\VM** 폴더에 추출합니다. 

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 **Hyper-V 관리자** 콘솔로 전환한 후 **az30308a-hv-vm** 노드를 선택하고 **가상 머신 가져오기**를 선택합니다. 그러면 **가상 머신 가져오기** 마법사가 시작됩니다.

1. **가상 머신 가져오기** 마법사의 **시작하기 전에** 페이지에서 **다음 >** 을 클릭합니다.

1. **가상 머신 가져오기** 마법사의 **폴더 찾기** 페이지에서 추출된 **Virtual Machines** 폴더의 위치를 지정하고 **다음 >** 을 선택합니다.

1. **가상 머신 가져오기** 마법사의 **가상 머신 선택** 페이지에서 **다음 >** 을 선택합니다.

1. **가상 머신 가져오기** 마법사의 **가져오기 형식 선택** 페이지에서 **가상 머신 등록(기존 고유 ID 사용)** 을 선택한 후 **다음 >** 을 선택합니다.

1. **가상 머신 가져오기** 마법사의 **프로세서 구성** 페이지에서 **가상 프로세서 수**를 **4**로 설정한 후 **다음 >** 을 선택합니다.

   >**참고**: 가상 프로세서 수의 변경 내용을 언급하는 오류 메시지가 나타나면 무시합니다.

1. **가상 머신 가져오기** 마법사의 **네트워크 연결** 페이지에서 **연결** 드롭다운 목록에 있는 **NestedSwitch**를 선택한 후 **다음 >** 을 선택합니다.

1. **가상 머신 가져오기** 마법사의 **요약** 페이지에서 **완료**를 선택합니다.

   >**참고**: 가져오기가 완료될 때까지 기다립니다. 완료되려면 약 10분이 소요됩니다.

1. **Hyper-V 관리자** 콘솔에서 새로 가져온 가상 머신을 선택한 후 **이름 바꾸기**를 선택하고 이름을 **az30308a-vma1**로 설정합니다.

1. **Hyper-V 관리자** 콘솔에서 새로 가져온 가상 머신을 선택하고 **시작**을 선택합니다. 

1. **Hyper-V 관리자** 콘솔에서 가상 머신이 실행 중인지 확인하고 **연결**을 선택합니다. 

1. 가상 어플라이언스에 대한 가상 머신 연결 창의 **라이선스 용어** 페이지에서 **수락**을 선택합니다. 

1. 가상 어플라이언스에 대한 가상 머신 연결 창의 **사용자 지정 설정** 페이지에서 기본 제공 관리자 계정의 암호를 **Pa55w.rd1234**로 설정하고 **완료**를 선택합니다. 

1. 가상 어플라이언스에 대한 가상 머신 연결 창에서 새로 설정된 암호를 사용하여 로그인합니다.

1. 가상 어플라이언스에 대한 가상 머신 연결 창 내에서 Windows PowerShell을 시작하고 다음을 실행하여 해당 IP 주소를 식별합니다.

   ```powershell
   (Get-NetIPAddress).IPAddress
   ```

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 Microsoft Edge를 다운로드한 후 기본 설정을 사용하여 설치를 실행합니다.

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내 Microsoft Edge 창에서 [https://`IPaddress`:44368](https://`IPaddress`:44368)로 이동합니다. 여기서 `IPaddress` 자리 표시자는 이전 단계에서 식별된 IP 주소를 나타냅니다.

   >**참고**: 웹 사이트 보안 인증서 관련 경고는 무시하면 됩니다. 

1. 메시지가 표시되면 다음 자격 증명으로 로그인합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 사용자 이름 | **관리자** |
    | 암호 | **Pa55w.rd1234** |

1. Microsoft Edge 창 내의 **어플라이언스 구성 관리자** 페이지에서 **동의함** 단추를 선택하고 필수 구성 요소가 확인될 때까지 기다린 후에 **계속**을 선택합니다. 

1. Microsoft Edge 창 내 **어플라이언스 구성 관리자** 페이지 **Azure Migrate 등록** 섹션에 있는 **Azure Migrate 프로젝트 키 입력** 텍스트 상자에 이 연습 앞부분에서 메모장에 복사한 키를 붙여넣고, **로그인**을 선택하고, 표시된 기본 키를 적용하여 클립보드에 복사합니다. 그런 다음 **코드 복사 및 로그인**을 브라우저 창의 **코드 입력** 창에서 클립보드에 복사한 코드를 붙여넣고 선택한 후 **다음**을 선택합니다. 다음으로, 이 랩에서 사용 중인 구독의 Owner 역할인 사용자 계정 자격 증명을 입력하여 로그인한 후 브라우저 페이지를 닫습니다. 

1. Microsoft Edge 창 내 **어플라이언스 구성 관리자** 페이지에서 등록이 정상적으로 완료되었는지 확인하고 **계속**을 선택합니다. 

1. Microsoft Edge 창 내 **어플라이언스 구성 관리자** 페이지의 **자격 증명 및 검색 원본 관리** 섹션에서 **자격 증명 추가**를 선택하고 **자격 증명 추가** 창에서 다음 설정을 지정한 후에 **저장**을 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 식별 이름 | **az30308acreds** |    
    | 사용자 이름 | **학생** |
    | 암호 | **Pa55w.rd1234** |

1. Microsoft Edge 창 내 **어플라이언스 구성 관리자** 페이지의 **Hyper-V 호스트/클러스터 세부 정보 입력** 섹션에서 **검색 원본 추가**를 선택합니다. 그런 다음 **검색 원본 추가** 창에서 **단일 항목 추가** 옵션을 선택하고 **검색 원본** 드롭다운 목록의 옵션이 **Hyper-V 호스트/클러스터**로 설정되어 있는지 확인합니다. 그런 후 **이름** 드롭다운 목록에서 **az30308acreds** 항목을 선택하고 **IP 주소/FQDN** 텍스트 상자에 **10.0.2.1**를 입력한 다음 **저장**을 선택합니다. 

1. Microsoft Edge 창 내 **어플라이언스 구성 관리자** 페이지의 **Hyper-V 호스트/클러스터 세부 정보 입력** 섹션에서 **검색 시작**을 선택합니다. 

   >**참고**: 일반적으로 검색된 서버의 메타데이터가 Azure Portal에 표시되려면 호스트당 약 15분이 걸릴 수 있습니다.


#### 작업 2: 평가 구성, 실행 및 보기

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 Azure Portal을 표시하는 Microsoft Edge 창을 통해 **Azure Migrate | 서버** 블레이드로 다시 이동하여 **새로 고침**을 선택하고 **Azure Migrate: 서버 평가** 타일에서 **평가**를 선택합니다.

   >**참고**: 페이지를 다시 새로 고쳐야 할 수 있습니다. 

1. **평가 속성** 블레이드에서 **편집**을 선택한 후 다음 설정을 지정하고(기타 설정은 기본값 유지) **저장**을 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 대상 위치 | 이 랩에서 사용 중인 Azure 지역의 이름 |
    | 스토리지 유형 | **자동** |
    | 예약된 인스턴스 | **예약된 인스턴스 없음** |
    | 크기 조정 기준 | **온-프레미스와 같이** |
    | VM 시리즈 | **Dsv3_series** |
    | 쾌적 인자 | **1** |
    | 제안 | **종량제** |
    | 통화 | 미국 달러($) | 
    | 할인 | **0** |
    | VM 작동 시간 | 월별 **31**일 및 일별 **24**시간| 

   >**참고**: 랩 환경에 내재된 제한 시간을 고려할 때 이 경우 실행 가능한 유일한 옵션은 **온-프레미스** 평가입니다. 

1. **서버 평가** 블레이드로 돌아와 **다음**을 선택한 후 **평가할 머신 선택** 탭으로 이동합니다.

1. **평가 이름**을 **az30308a-assessment**로 설정합니다.

1. **새로 만들기** 옵션이 선택되어 있는지 확인하고 그룹 이름을 **az30308a-assessment-group**으로 설정한 후 그룹에 추가할 머신 목록에서 **az30308a-vm1**을 선택합니다.

1. **다음**, **평가 만들기**를 차례로 클릭합니다. 

1. **Azure Migrate | 서버** 블레이드로 다시 이동하여 **새로 고침**을 선택하고 **Azure Migrate: 서버 평가** 타일에서 **평가** 라인에 **1**개의 항목이 포함되어 있는지 확인한 후 선택합니다.

1. **Azure Migrate: 서버 평가 | 평가** 블레이드에서 새로 만든 평가인 **az30308a-assessment**를 선택합니다. 

1. **az30308a-assessment** 블레이드에서 컴퓨팅 및 스토리지 모두에 대한 Azure 준비 상태 및 월간 비용 추정치를 나타내는 정보를 검토합니다. 

   >**참고**: 실제 시나리오에서는 평가 단계에서 서버 종속성에 대한 더 많은 통찰력을 제공하기 위해 종속성 에이전트를 설치하는 것이 좋습니다.


### 연습 3: Azure Migrate를 사용하여 Hyper-V VM 마이그레이션
  
이 연습의 주요 작업은 다음과 같습니다.

1. Hyper-V VM 마이그레이션 준비

1. Hyper-V VM의 복제 구성

1. Hyper-V VM 마이그레이션 수행

1. 랩에 배포된 Azure 리소스 제거


#### 작업 1: Hyper-V VM 마이그레이션 준비

1. **az30308a-hv-vm**에 대한 원격 데스크톱 세션 내에서 Azure Portal을 표시하는 Microsoft Edge 창을 통해 **Azure Migrate | 서버** 블레이드로 다시 이동합니다. 

1. **Azure Migrate | 서버** 블레이드로 돌아가서 **Azure Migrate: 서버 마이그레이션** 타일에서 **검색** 링크를 선택합니다. 

1. **컴퓨터 검색** 블레이드에서 다음 설정을 지정하고(기타 설정은 기본값 유지) **리소스 만들기**를 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 컴퓨터가 가상화되어 있습니까? | **예, Hyper-V 사용** |
    | 대상 지역 | 이 랩에서 사용 중인 Azure 지역의 이름 | 
    | 마이그레이션 대상 지역을 확인 | 선택 |

    >**참고**: 이 단계에서는 Azure Site Recovery 자격 증명 모음의 프로비전을 자동으로 트리거합니다.

1. **1. Hyper-V 호스트 서버 준비** 단계의 **컴퓨터 검색** 블레이드에서 첫 번째 **다운로드** 링크(다운로드 단추 아님)를 선택하여 Hyper-V 복제 공급자 소프트웨어 설치 프로그램을 다운로드합니다.

1. 메시지가 표시되면 **AzureSiteRecoveryProvider.exe**를 실행합니다. 이렇게 하면 **Azure Site Recovery 공급자 설정(Hyper-V 서버)** 마법사가 시작됩니다.

1. **Microsoft 업데이트** 페이지에서 **끄기**를 선택한 후 **다음**을 선택합니다.

1. **공급자 설치** 페이지에서 **설치**를 클릭합니다.

1. Azure Portal로 전환하고 **컴퓨터 검색** 블레이드에서 온-프레미스 Hyper-V 호스트 준비 절차의 1단계에 있는 **다운로드** 단추를 선택하여 자격 증명 모음 등록 키를 다운로드합니다. 메시지가 표시되면 **다운로드** 폴더에 등록 키를 저장합니다.

1. **공급자 설치** 페이지로 전환하고 **등록**을 선택합니다. 이렇게 하면 **Microsoft Azure Site Recovery 등록 마법사**가 시작됩니다.

1. **Microsoft Azure Site Recovery 등록 마법사**의 **자격 증명 모음 설정** 페이지에서 **찾아보기**를 선택하고 **다운로드** 폴더로 이동하여 보관 자격 증명 파일을 선택하고 **열기**를 선택합니다.

1. **Microsoft Azure Site Recovery 등록 마법사**의 **자격 증명 모음 설정** 페이지로 돌아가서 **다음**을 선택합니다.

1. **Microsoft Azure Site Recovery 등록 마법사**의 **프록시 설정** 페이지에서 기본 설정을 수락하고 **다음**을 선택합니다.

1. **Microsoft Azure Site Recovery 등록 마법사**의 **등록** 페이지에서**완료**를 선택합니다.

1. 등록 프로세스가 완료되면 **컴퓨터 검색** 블레이드에서 **등록 완료**를 선택합니다.

   >**참고**: **컴퓨터 검색** 블레이드를 표시하는 브라우저 페이지를 새로 고친 후 다시 돌아가야 할 수도 있습니다.

   >**참고**: 가상 머신 검색을 완료하는 데 최대 15분이 걸릴 수 있습니다.


#### 작업 2: Hyper-V VM의 복제 구성

1. 등록이 완료되었다는 확인을 받으면 **Azure Migrate**으로 다시 이동합니다. **| 서버** 블레이드로 돌아가서 **Azure Migrate: 서버 마이그레이션** 타일에서, **복제** 링크를 선택합니다. 

   >**참고**: **Azure Migrate | 서버** 블레이드를 표시하는 브라우저 페이지를 새로 고쳐야 할 수 있습니다.

1. **복제** 블레이드의 **원본 설정** 페이지에 있는 **컴퓨터가 가상화되어 있습니까?** 드롭다운 목록에서 **예, Hyper-V 사용**을 선택하고 **다음: 가상 머신**을 선택합니다.  

1. **복제** 블레이드의 **가상 머신** 페이지에서 다음 설정을 지정하고(기타 설정은 기본값 유지) **다음:**을 선택합니다.** 다음: 대상 설정**을 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | Azure Migrate 평가에서 마이그레이션 설정 가져오기 | **예, Azure Migrate 평가에서 마이그레이션 설정을 적용합니다.** |
    | 그룹 선택 | **az30308a-assessment-group** |
    | 평가 선택 | **az30308a-assessment** |
    | 가상 머신 | **az30308a-vm1** |

1. **복제** 블레이드의 **대상 설정** 페이지에서 다음 설정을 지정하고(기타 설정은 기본값 유지) **다음**: **컴퓨팅**을 선택합니다.

    | 설정 | 값 | 
    | --- | --- |
    | 구독 | 이 랩에서 사용 중인 Azure 구독의 이름 |
    | 리소스 그룹 | **az30308c-LabRG** |
    | 스토리지 계정 복제 | 이 랩의 앞부분에서 만든 스토리지 계정의 이름 | 
    | Virtual Network | **az30308c-migration-vnet** |
    | 서브넷 | **subnet0** |

1. **복제** 블레이드의 **컴퓨팅** 페이지에서, **Azure VM 크기** 드롭다운 목록에 **Standard_D2s_v3**이 선택되어 있는지 확인하고 **OS 유형** 드롭다운 목록에서 **Windows**를 선택한 후 **다음: 디스크**를 선택합니다.  

1. **복제** 블레이드의 **디스크** 페이지에서 기본 설정을 수락하고 **다음: 검토 + 복제 시작**을 선택합니다.  

1. **복제** 블레이드의 **검토 + 복제 시작** 페이지에서 **복제**를 선택합니다.  

1. 복제 상태를 모니터링하려면 **Azure Migrate | 서버** 블레이드로 돌아가서 **Azure Migrate: 서버 마이그레이션** 타일에서 **복제 서버** 항목을 선택한 후 **Azure Migrate: 모니터링할 수 있습니다. | 복제 컴퓨터**에서 복제 컴퓨터 목록의 **상태** 열을 검사합니다.

1. 상태가 **보호됨**으로 변경될 때까지 기다립니다. 이 경우 15분이 더 걸릴 수 있습니다.


#### 작업 3: Hyper-V VM 마이그레이션 수행

1. Azure Portal의 **Azure Migrate: 모니터링할 수 있습니다. | 복제 컴퓨터**에서 **az30308a-vm1** 가상 머신을 나타내는 항목을 선택합니다.

1. **az30308a-vm1** 복제 컴퓨터 블레이드에서 **테스트 마이그레이션**을 선택합니다.

1. **마이그레이션 테스트** 블레이드의 **Virtual Network** 드롭다운 목록에서 **az30308c-test-vnet**를 선택한 후 **테스트 마이그레이션**을 선택합니다.

   >**참고**: 테스트 마이그레이션이 완료될 때까지 기다립니다. 완료되려면 약 5분이 소요됩니다.

1. Azure Portal에서 **가상 머신**을 검색 및 선택하고 **가상 머신** 블레이드에서 새로 프로비전된 가상 머신 **az30308a-vm1-test**를 나타내는 항목을 기록합니다.

1. Azure Portal에서 **Azure Migrate: 모니터링할 수 있습니다. | 복제 컴퓨터**로 다시 이동하여 **새로 고침**을 선택하고 **az30308a-vm1** 가상 머신이 **테스트 장애 조치 정리 보류 중** 상태로 나열되어 있는지 확인합니다.

1. **Azure Migrate: 모니터링할 수 있습니다. | 복제 컴퓨터** 블레이드에서 **az30308a-vm1** 가상 머신을 나타내는 항목을 선택합니다.

1. **az30308a-vm1** 복제 컴퓨터 블레이드에서 **테스트 마이그레이션 정리**를 선택합니다.

1. **테스트 마이그레이션 정리** 블레이드에서 **테스트 완료** 체크박스를 선택합니다. **테스트 가상 머신 삭제**를 선택하고 **테스트 정리**를 선택합니다.

1. 테스트 장애 조치 정리 작업이 완료되면 **az30308a-vm1** 복제 컴퓨터 블레이드를 표시하는 브라우저 페이지를 새로 고치고 도구 모음의 **마이그레이션** 아이콘을 자동으로 사용할 수 있게 되었는지 확인합니다.

1. **az30308a-vm1** 복제 컴퓨터 블레이드에서 **마이그레이션** 링크를 선택합니다. 

1. **마이그레이션** 블레이드의 **데이터 손실을 최소화하기 위해 마이그레이션 전에 컴퓨터를 종료하나요?** 드롭다운 목록에서 **예**를 선탁한 후 **az30308a-vm1** 항목 옆에 있는 체크박스를 선택하고 **마이그레이션**을 선택합니다.

1. 마이그레이션 상태를 모니터링하려면 **Azure Migrate**로 다시 이동하여 **| 서버** 블레이드로 돌아가서 **Azure Migrate: 서버 마이그레이션** 타일에서 **복제 서버** 항목을 선택한 후 **Azure Migrate: 모니터링할 수 있습니다. | 복제 컴퓨터**에서 복제 컴퓨터 목록의 **상태** 열을 검사합니다. 상태가 **계획된 장애 조치(failover) 완료** 상태로 표시되는지 확인합니다.

   >**참고**: 마이그레이션은 되돌릴 수 없는 작업입니다. 선택한 정보를 확인하려면 Azure Migrate | 서버 블레이드로 다시 이동하고 페이지를 새로 고친 후 Azure Migrate: 서버 마이그레이션 타일의 마이그레이션된 서버 값이 1인지 확인합니다.


#### 작업 4: 랩에 배포된 Azure 리소스 제거

1. **az30308a-vm0**에 대한 원격 데스크톱 세션 내 Azure Portal을 표시하는 브라우저 창의 Cloud Shell 창 내에서 PowerShell 세션을 시작합니다.

1. Cloud Shell 창에서 다음을 실행하여 이 연습에서 만든 리소스 그룹을 나열합니다.

   ```powershell
   Get-AzResourceGroup -Name 'az30308*'
   ```

   > **참고**: 이 랩에서 만든 리소스 그룹만 출력에 포함되어 있는지 확인합니다. 이 작업에서는 이러한 그룹을 삭제할 것입니다.

1. Cloud Shell 창에서 다음을 실행하여 이 랩에서 만든 리소스 그룹을 삭제합니다.

   ```powershell
   Get-AzResourceGroup -Name 'az30308*' | Remove-AzResourceGroup -Force -AsJob
   ```

1. Cloud Shell 창을 닫습니다.
