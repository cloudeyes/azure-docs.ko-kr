---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/12/2019
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 23550c83e76631e44d5036e0a038f01b61a79f1b
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79208239"
---
## <a name="assign-access-permissions-to-an-identity"></a>ID에 액세스 권한 할당

Id 기반 인증을 사용 하 여 Azure Files 리소스에 액세스 하려면 id (사용자, 그룹 또는 서비스 사용자)에 게 공유 수준에서 필요한 권한이 있어야 합니다. 이 프로세스는 특정 사용자가 파일 공유에 대해 갖는 액세스 유형을 지정 하는 Windows 공유 사용 권한을 지정 하는 것과 비슷합니다. 이 섹션의 지침에서는 파일 공유에 대한 읽기, 쓰기 또는 삭제 권한을 ID에 할당하는 방법을 보여줍니다.

사용자에 게 공유 수준 권한을 부여 하기 위한 세 가지 Azure 기본 제공 역할이 도입 되었습니다.

- **저장소 파일 데이터 Smb 공유 판독기** 는 smb를 통한 Azure Storage 파일 공유에서 읽기 권한을 허용 합니다.
- **저장소 파일 데이터 Smb 공유 참가자** 는 smb를 통해 Azure Storage 파일 공유에 대 한 읽기, 쓰기 및 삭제 액세스를 허용 합니다.
- **저장소 파일 데이터 Smb 공유 승격 된 참가자** 는 SMB를 통한 Azure Storage 파일 공유에서 NTFS 읽기, 쓰기, 삭제 및 수정 권한을 허용 합니다.

> [!IMPORTANT]
> 파일 소유권을 가져오는 기능을 포함 하 여 파일 공유에 대 한 모든 관리 권한을 저장소 계정 키를 사용 해야 합니다. Azure AD 자격 증명으로는 관리 권한이 지원되지 않습니다.

Azure Portal, PowerShell 또는 Azure CLI를 사용 하 여 공유 수준 권한을 부여 하는 사용자의 Azure AD id에 기본 제공 역할을 할당할 수 있습니다.

> [!NOTE]
> 인증에 AD를 사용할 계획인 경우 AD 자격 증명을 Azure AD와 동기화 해야 합니다. AD에서 Azure AD로의 암호 해시 동기화는 선택 사항입니다. 공유 수준 권한은 AD에서 동기화 되는 Azure AD id에 부여 됩니다.

#### <a name="azure-portal"></a>Azure portal
[Azure Portal](https://portal.azure.com)를 사용 하 여 Azure AD ID에 RBAC 역할을 할당 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 파일 공유로 이동 하거나 [파일 공유를 만듭니다](../articles/storage/files/storage-how-to-create-file-share.md).
2. **Access Control(IAM)** 을 선택합니다.
3. **역할 할당 추가** 선택
4. **역할 할당 추가** 블레이드의 **역할** 목록에서 적절 한 기본 제공 역할 (저장소 파일 데이터 Smb 공유 판독기, 저장소 파일 데이터 smb 공유 참가자)을 선택 합니다. 기본 설정인 **AZURE AD 사용자, 그룹 또는 서비스 주체**에 대 한 **액세스 할당을** 그대로 둡니다. 이름 또는 전자 메일 주소를 기준으로 대상 Azure AD id를 선택 합니다.
5. **저장** 을 선택 하 여 역할 할당 작업을 완료 합니다.

#### <a name="powershell"></a>PowerShell

다음 PowerShell 샘플은 로그인 이름에 따라 Azure AD id에 RBAC 역할을 할당 하는 방법을 보여 줍니다. PowerShell을 사용하여 RBAC 역할을 할당하는 방법에 대한 자세한 내용은 [RBAC 및 Azure PowerShell을 사용하여 액세스 관리](../articles/role-based-access-control/role-assignments-powershell.md)를 참조하세요.

다음 샘플 스크립트를 실행 하기 전에 대괄호를 포함 하 여 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
다음 CLI 2.0 명령은 로그인 이름을 기반으로 하 여 Azure AD id에 RBAC 역할을 할당 하는 방법을 보여 줍니다. Azure CLI에서 RBAC 역할을 할당 하는 방법에 대 한 자세한 내용은 [rbac 및 Azure CLI를 사용 하 여 액세스 관리](../articles/role-based-access-control/role-assignments-cli.md)를 참조 하세요. 

다음 샘플 스크립트를 실행 하기 전에 대괄호를 포함 하 여 자리 표시자 값을 고유한 값으로 바꾸어야 합니다.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>SMB를 통한 NTFS 권한 구성 
RBAC를 사용하여 공유 수준 권한할당을 한 후에는 루트, 디렉터리 또는 파일 수준에서 적절한 NTFS 권한을 할당해야 합니다. 공유 수준 사용 권한은 사용자가 공유에 액세스할 수 있는지 여부를 결정 하는 상위 수준 게이트 키퍼로 생각 하면 됩니다. NTFS 권한은 사용자가 디렉터리 또는 파일 수준에서 수행할 수 있는 작업을 결정 하기 위해 보다 세분화 된 수준으로 작동 합니다.

Azure Files는 NTFS 기본 및 고급 권한의 전체 집합을 지원합니다. 공유를 탑재 한 다음 Windows 파일 탐색기를 사용 하거나 Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) 또는 [Set ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) 명령을 실행 하 여 Azure 파일 공유의 디렉터리 및 파일에 대 한 NTFS 권한을 확인 하 고 구성할 수 있습니다. 

수퍼유저 권한으로 NTFS를 구성 하려면 도메인에 가입 된 VM의 저장소 계정 키를 사용 하 여 공유를 탑재 해야 합니다. 다음 섹션의 지침에 따라 명령 프롬프트에서 Azure 파일 공유를 탑재 하 고 NTFS 사용 권한을 적절 하 게 구성 합니다.

파일 공유의 루트 디렉터리에서 지원되는 권한 집합은 다음과 같습니다.

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="configure-ntfs-permissions-with-icacls"></a>icacls를 사용하여 NTFS 권한 구성
루트 디렉터리를 비롯하여 파일 공유에 있는 모든 디렉터리와 파일에 대한 모든 권한을 부여하려면 다음 Windows 명령을 사용합니다. 예제의 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Icacls를 사용 하 여 NTFS 권한 및 지원 되는 다양 한 권한 유형을 설정 하는 방법에 대 한 자세한 내용은 [icacls 명령줄 참조](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls)를 참조 하세요.

### <a name="mount-a-file-share-from-the-command-prompt"></a>명령 프롬프트에서 파일 공유 탑재

Windows **net use** 명령을 사용하여 Azure 파일 공유를 탑재합니다. 다음 예제의 자리 표시자 값을 사용자 고유의 값으로 대체 해야 합니다. 파일 공유 탑재에 대 한 자세한 내용은 [Windows에서 Azure 파일 공유 사용](../articles/storage/files/storage-how-to-use-files-windows.md)을 참조 하세요.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Windows 파일 탐색기를 사용 하 여 NTFS 사용 권한 구성
Windows 파일 탐색기를 사용 하 여 루트 디렉터리를 포함 하 여 파일 공유 아래의 모든 디렉터리 및 파일에 대 한 모든 권한을 부여 합니다.

1. Windows 파일 탐색기를 열고 파일/디렉터리를 마우스 오른쪽 단추로 클릭 하 고 **속성** 을 선택 합니다.
2. **보안** 탭을 클릭 합니다.
3. **편집 ...** 을 클릭 합니다. 사용 권한을 변경 하는 단추
4. 기존 사용자의 사용 권한을 변경 하거나 **추가** ...를 클릭 하 여 새 사용자에 게 사용 권한을 부여할 수 있습니다.
5. 새 사용자 추가에 대 한 프롬프트 창에서 **선택할 개체 이름을 입력 하십시오** . 상자에 권한을 부여 하려는 대상 사용자 이름을 입력 하 고 **이름 확인** 을 클릭 하 여 대상 사용자의 전체 UPN 이름을 찾습니다.
7.  설정 메뉴에서 **확인**
8.  보안 탭에서 새로 추가 하는 사용자에 게 부여 하려는 모든 사용 권한을 선택 합니다.
9.  **적용**을 클릭합니다.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>도메인 조인 VM에서 파일 공유 탑재

다음 프로세스는 파일 공유 및 액세스 권한이 올바르게 설정 되었는지 확인 하 고 도메인에 가입 된 VM에서 Azure 파일 공유에 액세스할 수 있는지 확인 합니다.

다음 그림에 표시 된 것 처럼 권한을 부여한 Azure AD id를 사용 하 여 VM에 로그인 합니다. Azure Files에 대해 AD 인증을 사용 하도록 설정한 경우 AD 자격 증명을 사용 합니다. Azure AD DS 인증의 경우 Azure AD 자격 증명을 사용 하 여 로그인 합니다.

![사용자 인증용 Azure AD 로그인 화면이 표시된 스크린샷](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

다음 명령을 사용 하 여 Azure 파일 공유를 탑재 합니다. 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다. 인증 되었으므로 저장소 계정 키, AD 자격 증명 또는 Azure AD 자격 증명을 제공할 필요가 없습니다. Single sign-on 환경은 AD 또는 Azure AD DS 인증에 대해 지원 됩니다.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```
