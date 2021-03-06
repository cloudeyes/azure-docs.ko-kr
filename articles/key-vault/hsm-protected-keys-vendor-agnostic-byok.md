---
title: Azure Key Vault에 HSM 보호 키를 생성하고 전송하는 방법 - Azure Key Vault | Microsoft Docs
description: 이 문서를 사용 하 여 Azure Key Vault와 함께 사용할 사용자 고유의 HSM 보호 키를 계획, 생성 및 전송 하는 데 도움을 줍니다. BYOK (사용자 고유의 키로 가져오기) 라고도 합니다.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: ambapat
ms.openlocfilehash: 08a4330f4a786deca8ddb2f1c6803b29152e7f50
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/10/2020
ms.locfileid: "79080137"
---
# <a name="import-hsm-protected-keys-to-key-vault-preview"></a>Key Vault로 HSM 보호 키 가져오기(미리 보기)

> [!NOTE]
> 이 기능은 미리 보기 상태 이며 Azure 지역 *동부 2 EUAP* 및 *미국 중부 EUAP*에서만 사용할 수 있습니다. 

Azure Key Vault 사용 하는 경우 보증을 추가 하기 위해 HSM (하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 키가 HSM 경계를 벗어날 수 없습니다. 이 시나리오를 종종 byok (사용자 *고유의 키로 가져오기* ) 라고 합니다. Key Vault는 nCipher nShield 제품군 (FIPS 140-2 Level 2 유효성 검사)을 사용 하 여 키를 보호 합니다.

이 문서의 정보를 사용 하 여 Azure Key Vault와 함께 사용할 사용자 고유의 HSM 보호 키를 계획, 생성 및 전송할 수 있습니다.

> [!NOTE]
> Azure 중국 21Vianet에는이 기능을 사용할 수 없습니다. 
> 
> 이 가져오기 방법은 [지원 되는 hsm](#supported-hsms)에 대해서만 사용할 수 있습니다. 

자세한 내용 및 Key Vault 사용을 시작 하는 자습서는 (HSM 보호 키에 대 한 주요 자격 증명 모음을 만드는 방법 포함)을 참조 하세요. [Azure Key Vault 이란?](key-vault-overview.md)를 참조 하세요.

## <a name="overview"></a>개요

프로세스에 대 한 개요는 다음과 같습니다. 완료할 특정 단계는이 문서의 뒷부분에 설명 되어 있습니다.

* Key Vault에서 키 (KEK ( *키 교환 키* ) 라고도 함)를 생성 합니다. KEK는 `import` 키 작업만 포함 하는 RSA-HSM 키 여야 합니다. Key Vault Premium SKU만 RSA-HSM 키를 지원 합니다.
* KEK 공개 키를 pem 파일로 다운로드 합니다.
* KEK 공개 키를 온-프레미스 HSM에 연결 된 오프 라인 컴퓨터로 전송 합니다.
* 오프 라인 컴퓨터에서 HSM 공급 업체에서 제공한 BYOK 도구를 사용 하 여 BYOK 파일을 만듭니다. 
* 대상 키는 Key Vault HSM으로 전송 될 때까지 암호화 상태를 유지 하는 KEK로 암호화 됩니다. 암호화 된 버전의 키만 온-프레미스 HSM을 벗어납니다.
* Key Vault HSM 내에서 생성 된 KEK는 내보낼 수 없습니다. Hsm은 Key Vault HSM 외부에 존재 하지 않는 KEK의 일반 버전이 없다는 규칙을 적용 합니다.
* KEK는 대상 키를 가져올 동일한 키 자격 증명 모음에 있어야 합니다.
* BYOK 파일이 Key Vault에 업로드 되 면 Key Vault HSM은 KEK 개인 키를 사용 하 여 대상 키 자료를 해독 하 고 HSM 키로 가져옵니다. 이 작업은 Key Vault HSM 내에서 완전히 발생 합니다. 대상 키는 항상 HSM 보호 경계에 남아 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 표에서는 Azure Key Vault BYOK를 사용 하기 위한 필수 구성 요소를 나열 합니다.

| 요구 사항 | 자세한 정보 |
| --- | --- |
| Azure 구독 |Azure Key Vault에서 key vault를 만들려면 Azure 구독이 필요 합니다. [무료 평가판에 등록](https://azure.microsoft.com/pricing/free-trial/)하세요. |
| HSM 보호 된 키를 가져오기 위한 Key Vault Premium SKU |Azure Key Vault의 서비스 계층 및 기능에 대 한 자세한 내용은 [Key Vault 가격 책정](https://azure.microsoft.com/pricing/details/key-vault/)을 참조 하세요. |
| 지원 되는 hsm 목록 및 BYOK 도구 및 HSM 공급 업체에서 제공 하는 지침의 HSM | Hsm을 사용 하는 방법에 대 한 기본 지식 및 HSM에 대 한 권한이 있어야 합니다. [지원 되는 hsm](#supported-hsms)을 참조 하세요. |
| Azure CLI 버전 2.1.0 이상 | [Azure CLI 설치를](/cli/azure/install-azure-cli?view=azure-cli-latest)참조 하세요.|

## <a name="supported-hsms"></a>지원 되는 Hsm

|공급 업체 이름|공급 업체 유형|지원 되는 HSM 모델|자세한 정보|
|---|---|---|---|
|Thales|제조업체|펌웨어 버전 7.3 이상을 사용 하는 Luna HSM 7 제품군| [Luna BYOK BYOK 도구 및 설명서](https://supportportal.thalesgroup.com/csm?id=kb_article_view&sys_kb_id=3892db6ddb8fc45005c9143b0b961987&sysparm_article=KB0021016)|
|Fortanix|HSM을 서비스로|자체 방어 키 관리 서비스 (SDKMS)|[BYOK에 대해 SDKMS 키를 클라우드 공급자로 내보내기-Azure Key Vault](https://support.fortanix.com/hc/en-us/articles/360040071192-Exporting-SDKMS-keys-to-Cloud-Providers-for-BYOK-Azure-Key-Vault)|


> [!NOTE]
> HSM 보호 된 키를 nCipher nShield 집합에서 가져오려면 [레거시 BYOK 절차](hsm-protected-keys-legacy.md)를 사용 합니다.

## <a name="supported-key-types"></a>지원 되는 키 유형

|키 이름|키 유형|키 크기|원본|Description|
|---|---|---|---|---|
|키 교환 키 (KEK)|RSA| 2048 비트<br />3072 비트<br />4096 비트|Azure Key Vault HSM|Azure Key Vault에서 생성 된 HSM 지원 RSA 키 쌍|
|대상 키|RSA|2048 비트<br />3072 비트<br />4096 비트|공급 업체 HSM|Azure Key Vault HSM으로 전송할 키입니다.|

## <a name="generate-and-transfer-your-key-to-the-key-vault-hsm"></a>키를 생성 하 여 Key Vault HSM으로 전송 합니다.

키를 생성 하 여 Key Vault HSM으로 전송 하려면 다음을 수행 합니다.

* [1 단계: KEK 생성](#step-1-generate-a-kek)
* [2 단계: KEK 공개 키 다운로드](#step-2-download-the-kek-public-key)
* [3 단계: 전송할 키 생성 및 준비](#step-3-generate-and-prepare-your-key-for-transfer)
* [4 단계: Azure Key Vault에 키 전송](#step-4-transfer-your-key-to-azure-key-vault)

### <a name="step-1-generate-a-kek"></a>1 단계: KEK 생성

KEK는 Key Vault HSM에서 생성 되는 RSA 키입니다. KEK는 가져오려는 키 ( *대상* 키)를 암호화 하는 데 사용 됩니다.

KEK는 다음과 같아야 합니다.
- RSA HSM 키 (2048 비트, 3072 비트 또는 4096 비트)
- 대상 키를 가져오려는 동일한 키 자격 증명 모음에 생성 됩니다.
- 허용 된 키 작업이 `import`로 설정 된 상태에서 만들어짐

> [!NOTE]
> KEK에는 유일 하 게 허용 되는 키 작업으로 ' 가져오기 '가 있어야 합니다. ' import '는 다른 모든 키 작업과 함께 사용할 수 없습니다.

[Az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) 명령을 사용 하 여 `import`로 설정 된 키 작업이 있는 KEK를 만듭니다. 다음 명령에서 반환 된 키 식별자 (`kid`)를 기록 합니다. [3 단계](#step-3-generate-and-prepare-your-key-for-transfer)에서 `kid` 값을 사용 합니다.

```azurecli
az keyvault key create --kty RSA-HSM --size 4096 --name KEKforBYOK --ops import --vault-name ContosoKeyVaultHSM
```

### <a name="step-2-download-the-kek-public-key"></a>2 단계: KEK 공개 키 다운로드

[Az keyvault key download](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-download) 를 사용 하 여 KEK 공개 키를 pem 파일로 다운로드 합니다. 가져오는 대상 키는 KEK 공개 키를 사용 하 여 암호화 됩니다.

```azurecli
az keyvault key download --name KEKforBYOK --vault-name ContosoKeyVaultHSM --file KEKforBYOK.publickey.pem
```

KEKforBYOK. publickey 파일을 오프 라인 컴퓨터로 전송 합니다. 이 파일은 다음 단계에서 필요 합니다.

### <a name="step-3-generate-and-prepare-your-key-for-transfer"></a>3 단계: 전송할 키 생성 및 준비

BYOK 도구를 다운로드 하 고 설치 하려면 HSM 공급 업체의 설명서를 참조 하세요. HSM 공급 업체의 지침에 따라 대상 키를 생성 한 다음 키 전송 패키지 (BYOK 파일)를 만듭니다. BYOK 도구는 [1 단계의](#step-1-generate-a-kek) `kid`를 사용 하 고 [2 단계](#step-2-download-the-kek-public-key) 에서 다운로드 한 kekforbyok. publickey 파일을 사용 하 여 byok 파일에 암호화 된 대상 키를 생성 합니다.

BYOK 파일을 연결 된 컴퓨터로 전송 합니다.

> [!NOTE] 
> RSA 1024 비트 키를 가져오는 것은 지원 되지 않습니다. 현재 EC (타원 Curve) 키 가져오기는 지원 되지 않습니다.
> 
> **알려진 문제**: V7.4.0 Enet Luna HSM에서 RSA 4k 대상 키 가져오기는 펌웨어 이상 에서만 지원 됩니다.

### <a name="step-4-transfer-your-key-to-azure-key-vault"></a>4 단계: Azure Key Vault에 키 전송

키 가져오기를 완료 하려면 연결이 끊어진 컴퓨터에서 인터넷에 연결 된 컴퓨터로 키 전송 패키지 (BYOK 파일)를 전송 합니다. [Az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) 명령을 사용 하 여 byok 파일을 Key Vault HSM에 업로드 합니다.

```azurecli
az keyvault key import --vault-name ContosoKeyVaultHSM --name ContosoFirstHSMkey --byok-file KeyTransferPackage-ContosoFirstHSMkey.byok
```

업로드가 성공 하면 Azure CLI 가져온 키의 속성을 표시 합니다.

## <a name="next-steps"></a>다음 단계

이제 주요 자격 증명 모음에서 이 HSM 보호된 키를 사용할 수 있습니다. 자세한 내용은 [이 가격 및 기능 비교](https://azure.microsoft.com/pricing/details/key-vault/)를 참조 하세요.



