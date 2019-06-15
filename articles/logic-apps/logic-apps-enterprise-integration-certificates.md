---
title: Zabezpieczanie komunikatów B2B z certyfikatami — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodawanie certyfikatów do zabezpieczenia komunikatów B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, LADocs
manager: jeconnoc
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.suite: integration
ms.topic: article
ms.date: 08/17/2018
ms.openlocfilehash: 38bc1615c0849a33ddfa5790a66fc05d681ce339
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66167175"
---
# <a name="secure-b2b-messages-with-certificates"></a>Zabezpieczanie komunikatów B2B przy użyciu certyfikatów

Gdy zachodzi potrzeba przechowywać poufne komunikacji B2B, można zabezpieczyć komunikacji B2B dla przedsiębiorstwa integracji aplikacji, w szczególności logic apps, dodając certyfikaty na koncie integracji. Certyfikaty są dokumentów cyfrowych, sprawdź tożsamości dla uczestników łączności elektronicznej, które pomagają w bezpiecznej komunikacji w następujący sposób:

* Szyfrowanie zawartości komunikatu.
* Podpisuj cyfrowo wiadomości. 

W ramach aplikacji integracji przedsiębiorstwa, można użyć tych certyfikatów:

* [Certyfikaty publiczne](https://en.wikipedia.org/wiki/Public_key_certificate), którego należy to zrobić z publicznej sieci internet [certyfikatu urzędu certyfikacji](https://en.wikipedia.org/wiki/Certificate_authority) , ale nie wymagają żadnych kluczy. 

* Certyfikaty prywatne lub [ *certyfikaty z podpisem własnym*](https://en.wikipedia.org/wiki/Self-signed_certificate), które można utworzyć i wystawić samodzielnie, ale wymagają również klucze prywatne. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="upload-a-public-certificate"></a>Przekazywanie certyfikatu publicznego

Aby użyć *certyfikatu publicznego* w usłudze logic apps, które mają możliwości B2B usługi, należy najpierw przekazać certyfikat na koncie integracji. Po zdefiniowaniu właściwości w [umów](logic-apps-enterprise-integration-agreements.md) , tworzenia, certyfikat jest dostępny zabezpieczyć swoje komunikatów B2B.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W głównym menu platformy Azure, wybierz **wszystkie zasoby**. W polu wyszukiwania wprowadź nazwę konta integracji, a następnie wybierz żądane konto integracji.

   ![Znajdź i wybierz swoje konto integracji](media/logic-apps-enterprise-integration-certificates/select-integration-account.png)  

2. W obszarze **składniki**, wybierz **certyfikaty** kafelka.

   ![Wybierz pozycję "Certyfikaty"](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

3. W obszarze **certyfikaty**, wybierz **Dodaj**. W obszarze **Dodaj certyfikat**, podaj te szczegóły certyfikatu. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | <*certificate-name*> | Nazwa certyfikatu, który jest "publicCert", w tym przykładzie | 
   | **Typ certyfikatu** | Public | Typ certyfikatu |
   | **Certyfikat** | <*certificate-file-name*> | Aby znaleźć i wybrać plik certyfikatu, który chcesz przekazać, wybierz ikonę folderu **certyfikatu** pole. |
   ||||

   ![Wybieranie pozycji "Dodaj", podaj szczegóły certyfikatu](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

   Po wybór jest weryfikowana w Azure, Azure przekazanie danego certyfikatu.

   ![Azure wyświetla nowy certyfikat](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png) 

## <a name="upload-a-private-certificate"></a>Przekaż certyfikat prywatny

Aby użyć *certyfikatu prywatnego* w usłudze logic apps, które mają możliwości B2B usługi, należy najpierw przekazać certyfikat na koncie integracji. Musisz również mieć klucz prywatny, należy najpierw dodać do [usługi Azure Key Vault](../key-vault/key-vault-get-started.md). 

Po zdefiniowaniu właściwości w [umów](logic-apps-enterprise-integration-agreements.md) , tworzenia, certyfikat jest dostępny zabezpieczyć swoje komunikatów B2B.

> [!NOTE]
> W przypadku certyfikatów prywatnych, upewnij się, dodawania odpowiedniego certyfikatu publicznego, który pojawia się w [umowy AS2](logic-apps-enterprise-integration-as2.md) **wysyłania i odbierania** ustawień podpisywania i szyfrowania wiadomości.

1. [Dodaj klucz prywatny do usługi Azure Key Vault](../key-vault/certificate-scenarios.md#import-a-certificate) i podaj **klucz o nazwie**.
   
2. Zezwól aplikacji logiki platformy Azure do wykonywania operacji w usłudze Azure Key Vault. Aby udzielić dostępu do jednostki usługi Logic Apps, należy użyć polecenia programu PowerShell [AzKeyVaultAccessPolicy zestaw](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), na przykład:

   `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). W głównym menu platformy Azure, wybierz **wszystkie zasoby**. W polu wyszukiwania wprowadź nazwę konta integracji, a następnie wybierz żądane konto integracji.

   ![Znajdź swoje konto integracji](media/logic-apps-enterprise-integration-certificates/select-integration-account.png) 

4. W obszarze **składniki**, wybierz **certyfikaty** kafelka.  

   ![Wybierz Kafelek certyfikatów](media/logic-apps-enterprise-integration-certificates/add-certificates.png)

5. W obszarze **certyfikaty**, wybierz **Dodaj**. W obszarze **Dodaj certyfikat**, podaj te szczegóły certyfikatu. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

   | Właściwość | Wartość | Opis | 
   |----------|-------|-------------|
   | **Nazwa** | <*certificate-name*> | Nazwa certyfikatu, który jest "privateCert", w tym przykładzie | 
   | **Typ certyfikatu** | Prywatna | Typ certyfikatu |
   | **Certyfikat** | <*certificate-file-name*> | Aby znaleźć i wybrać plik certyfikatu, który chcesz przekazać, wybierz ikonę folderu **certyfikatu** pole. | 
   | **Grupa zasobów** | <*integration-account-resource-group*> | Grupa zasobów konta integracji, czyli "MyResourceGroup" w tym przykładzie | 
   | **Usługa Key Vault** | <*key-vault-name*> | Nazwa Twojego magazynu kluczy Azure |
   | **Nazwa klucza** | <*key-name*> | Nazwa klucza usługi |
   ||||

   ![Wybieranie pozycji "Dodaj", podaj szczegóły certyfikatu](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

   Po wybór jest weryfikowana w Azure, Azure przekazanie danego certyfikatu.

   ![Azure wyświetla nowy certyfikat](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png) 

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie umowy z B2B](logic-apps-enterprise-integration-agreements.md)
