---
title: 데이터 암호화-Azure Portal-Azure Database for MySQL
description: Azure Portal를 사용 하 여 Azure Database for MySQL에 대 한 데이터 암호화를 설정 하 고 관리 하는 방법을 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 78a290b1e2984719645fb4d4ff253ab021a0826e
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79299042"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 Azure Database for MySQL에 대 한 데이터 암호화

Azure Portal를 사용 하 여 Azure Database for MySQL 데이터 암호화를 설정 하 고 관리 하는 방법을 알아봅니다.

## <a name="prerequisites-for-azure-cli"></a>Azure CLI에 대 한 필수 구성 요소

* Azure 구독 및 해당 구독에 대한 관리자 권한이 있어야 합니다.
* Azure Key Vault에서, 고객이 관리 하는 키에 사용할 키 자격 증명 모음 및 키를 만듭니다.
* 키 자격 증명 모음에는 고객이 관리 하는 키로 사용할 수 있는 다음과 같은 속성이 있어야 합니다.
  * [일시 삭제](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [보호 된 데이터 삭제](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* 키에는 고객 관리 키로 사용할 다음 특성이 있어야 합니다.
  * 만료 날짜 없음
  * 사용 안 함 없음
  * 가져오기, 키 래핑, 키 래핑 해제 작업을 수행할 수 있습니다.

## <a name="set-the-right-permissions-for-key-operations"></a>키 작업에 대 한 올바른 사용 권한 설정

1. Key Vault에서 **액세스** 정책 > **액세스 정책 추가**를 선택 합니다.

   ![액세스 정책을 사용 하는 Key Vault의 스크린샷 강조 표시 된 액세스 정책 추가](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. **키 사용 권한**을 선택 하 고 **가져오기**, **래핑**, **래핑**해제, **보안 주체**(MySQL 서버의 이름)를 차례로 선택 합니다. 기존 보안 주체 목록에서 서버 보안 주체를 찾을 수 없는 경우 등록 해야 합니다. 처음으로 데이터 암호화를 설정 하려고 할 때 서버 보안 주체를 등록 하 라는 메시지가 표시 되 고 실패 합니다.

   ![액세스 정책 개요](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **저장**을 선택합니다.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Azure Database for MySQL에 대 한 데이터 암호화 설정

1. Azure Database for MySQL에서 **데이터 암호화** 를 선택 하 여 고객 관리 키를 설정 합니다.

   ![데이터 암호화가 강조 표시 된 Azure Database for MySQL의 스크린샷](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. 키 자격 증명 모음 및 키 쌍을 선택 하거나 키 식별자를 입력할 수 있습니다.

   ![데이터 암호화 옵션이 강조 표시 된 Azure Database for MySQL의 스크린샷](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **저장**을 선택합니다.

4. 임시 파일을 포함 하 여 모든 파일이 완전히 암호화 되었는지 확인 하려면 서버를 다시 시작 합니다.

## <a name="restore-or-create-a-replica-of-the-server"></a>서버 복제본 복원 또는 만들기

Key Vault에 저장 된 고객의 관리 키를 사용 하 여 Azure Database for MySQL 암호화 된 후에는 새로 만든 서버 복사본도 암호화 됩니다. 로컬 또는 지역 복원 작업을 통해 또는 복제본 (로컬/지역 간) 작업을 통해이 새 복사본을 만들 수 있습니다. 따라서 암호화 된 MySQL 서버의 경우 다음 단계를 사용 하 여 암호화 된 복원 된 서버를 만들 수 있습니다.

1. 서버에서 **개요** > **복원**을 선택 합니다.

   ![개요 및 복원이 강조 표시 된 Azure Database for MySQL의 스크린샷](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   또는 복제를 사용 하는 서버의 경우 **설정** 머리글 아래에서 **복제**를 선택 합니다.

   ![복제가 강조 표시 된 Azure Database for MySQL의 스크린샷](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. 복원 작업이 완료 되 면 새로 만든 서버가 기본 서버 키로 암호화 됩니다. 그러나 서버에서 기능 및 옵션을 사용할 수 없으며 서버에 액세스할 수 없습니다. 그러면 새 서버의 id에 키 자격 증명 모음에 대 한 액세스 권한이 아직 제공 되지 않았기 때문에 데이터 조작을 방지할 수 있습니다.

   ![액세스할 수 없음 상태가 강조 표시 된 Azure Database for MySQL의 스크린샷](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. 서버에 액세스할 수 있도록 하려면 복원 된 서버에서 키의 유효성을 다시 검사 합니다. **데이터 암호화** > **키 유효성**재검사를 선택 합니다.

   > [!NOTE]
   > 새 서버의 서비스 주체에 게 키 자격 증명 모음에 대 한 액세스 권한이 있어야 하기 때문에 첫 번째 유효성 재검사 시도는 실패 합니다. 서비스 주체를 생성 하려면 오류를 표시 하 고 서비스 주체를 생성 하는 **키 다시 유효성**검사를 선택 합니다. 이후에는이 문서 앞부분의 [이러한 단계](#set-the-right-permissions-for-key-operations) 를 참조 하세요.

   ![유효성 재검사 단계가 강조 표시 된 Azure Database for MySQL의 스크린샷](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   키 자격 증명 모음에 새 서버에 대 한 액세스 권한을 부여 해야 합니다.

4. 서비스 주체를 등록 한 후 키의 유효성을 다시 검사 하 고 서버에서 정상적인 기능을 다시 시작 합니다.

   ![복원 된 기능을 보여 주는 Azure Database for MySQL의 스크린샷](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Azure Resource Manager 템플릿을 사용 하 여 데이터 암호화 사용

Azure Portal 외에도 새 서버 및 기존 서버에 대해 Azure Resource Manager 템플릿을 사용 하 여 Azure Database for MySQL 서버에서 데이터 암호화를 사용 하도록 설정할 수 있습니다.

### <a name="for-a-new-server"></a>새 서버의 경우

미리 만든 Azure Resource Manager 템플릿 중 하나를 사용 하 여 데이터 암호화를 사용 하도록 설정 된 서버 프로 비전: [데이터 암호화 사용 예](https://github.com/Azure/azure-mysql/tree/master/arm-templates/ExampleWithDataEncryption)

이 Azure Resource Manager 템플릿은 Azure Database for MySQL 서버를 만들고 매개 변수로 전달 되는 키 **자격 증명 모음** 및 **키** 를 사용 하 여 서버에서 데이터 암호화를 사용 하도록 설정 합니다.

### <a name="for-an-existing-server"></a>기존 서버의 경우
또한 Azure Resource Manager 템플릿을 사용 하 여 기존 Azure Database for MySQL 서버에서 데이터 암호화를 사용 하도록 설정할 수 있습니다.

* 속성 개체의 `keyVaultKeyUri` 속성 아래에 이전에 복사한 Azure Key Vault 키의 URI를 전달 합니다.

* API 버전으로 *2020-01-01-preview* 를 사용 합니다.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforMySQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforMySQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforMySQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforMySQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}

```

## <a name="next-steps"></a>다음 단계

 데이터 암호화에 대 한 자세한 내용은 [고객 관리 키를 사용 하 여 데이터 암호화 Azure Database for MySQL](concepts-data-encryption-mysql.md)를 참조 하세요.
