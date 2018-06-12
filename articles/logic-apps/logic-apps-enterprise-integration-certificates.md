---
title: Zabezpieczanie komunikatów B2B z certyfikatami w usłudze Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodaj certyfikaty do zabezpieczenia wiadomości B2B z pakiet integracyjny dla przedsiębiorstw
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 7ba76a15792fe40b2a628b030f06930641d197a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299338"
---
# <a name="secure-b2b-messages-with-certificates"></a>Zabezpieczonych wiadomości B2B z certyfikatami

Czasami trzeba zachować poufne komunikacji B2B. Do zabezpieczenia komunikacji B2B w przedsiębiorstwie integracji aplikacji, w szczególności logikę aplikacji, należy dodać certyfikaty do konta integracji. Certyfikaty są dokumentami cyfrowe, które zweryfikować tożsamość uczestników łączności elektronicznej.
Certyfikaty pomóc zabezpieczyć komunikację w następujący sposób:

* Szyfrowania treści wiadomości
* Cyfrowe podpisywanie wiadomości  

Korzystanie z tych certyfikatów, w aplikacjach integracji przedsiębiorstwa:

* Certyfikaty publiczne, które muszą być zakupione od urzędu certyfikacji (CA).
* Certyfikaty prywatne, które można wystawić samodzielnie. Te certyfikaty są czasami określane jako certyfikaty z podpisem własnym.

## <a name="upload-a-public-certificate"></a>Przekaż certyfikat publiczny

Aby użyć *certyfikatu publicznego* w aplikacji logiki, które mają możliwości B2B, należy najpierw przekazać certyfikat do swojego konta integracji. Po zdefiniowaniu właściwości w [umowy](logic-apps-enterprise-integration-agreements.md) że tworzenia, certyfikat jest dostępny do zabezpieczenia wiadomości B2B.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W menu głównym Azure wybierz **wszystkie usługi**. W polu wyszukiwania wpisz "integrację", a następnie wybierz **konta integracji**.

   ![Znajdź konta integracji](media/logic-apps-enterprise-integration-certificates/overview-1.png)  

3. W obszarze **konta integracji**, wybierz konto integracji, w której chcesz dodać certyfikat.

   ![Wybierz konto integracji, do której chcesz dodać certyfikat](media/logic-apps-enterprise-integration-certificates/overview-3.png)  

4. Wybierz **certyfikaty** kafelka.  

   ![Wybierz pozycję "Certyfikaty"](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

5. W obszarze **certyfikaty**, wybierz **Dodaj**.

   ![Wybierz opcję "Dodaj"](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

6. W obszarze **Dodaj certyfikat**, podaj szczegóły certyfikatu.
   
   1. Wprowadź swój certyfikat **nazwa**. Typ certyfikatu wybierz **publicznego**.

   2. W prawej części **certyfikatu** wybierz ikonę folderu. 
   Znajdź i wybierz plik certyfikatu, który chcesz przekazać. 
   Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

      ![Przekaż certyfikat publiczny](media/logic-apps-enterprise-integration-certificates/certificate-3.png)

   Azure przekazywanie certyfikatu po weryfikacji wybór.

   ![Zobacz nowego certyfikatu](media/logic-apps-enterprise-integration-certificates/certificate-4.png) 

## <a name="upload-a-private-certificate"></a>Przekaż certyfikat prywatny

Aby użyć *certyfikatu prywatnego* w aplikacji logiki, które mają możliwości B2B, należy najpierw przekazać certyfikat do swojego konta integracji. Musisz również mieć klucz prywatny, który najpierw dodać do [usługi Azure Key Vault](../key-vault/key-vault-get-started.md). 

Po zdefiniowaniu właściwości w [umowy](logic-apps-enterprise-integration-agreements.md) że tworzenia, certyfikat jest dostępny do zabezpieczenia wiadomości B2B.

> [!NOTE]
> Dla prywatnych certyfikatów, upewnij się, że dodać odpowiedni certyfikat publiczny pojawią się w [umowy AS2](logic-apps-enterprise-integration-as2.md) wysyłania i odbierania ustawień podpisywania i szyfrowania wiadomości.

1. [Dodaj klucz prywatny do usługi Azure Key Vault](../key-vault/key-vault-get-started.md#add) i podaj **nazwa klucza**.
   
2. Autoryzacja aplikacje logiki platformy Azure do wykonywania operacji dla usługi Azure Key Vault. Aby udzielić dostępu do nazwy głównej usługi Logic Apps, użyj polecenia PowerShell [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy), na przykład:

   `Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName 
   '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`
 
3. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

4. W menu głównym Azure wybierz **wszystkie usługi**. W polu wyszukiwania wpisz "integrację", a następnie wybierz **konta integracji**.

   ![Znajdź konta integracji](media/logic-apps-enterprise-integration-certificates/overview-1.png) 

5. W obszarze **konta integracji**, wybierz konto integracji, w której chcesz dodać certyfikat.

6. Wybierz **certyfikaty** kafelka.  

   ![Wybierz Kafelek certyfikatów](media/logic-apps-enterprise-integration-certificates/certificate-1.png)

7. W obszarze **certyfikaty**, wybierz **Dodaj**.   

   ![Kliknij przycisk Dodaj](media/logic-apps-enterprise-integration-certificates/certificate-2.png)

8. W obszarze **Dodaj certyfikat**, podaj szczegóły certyfikatu.
   
   1. Wprowadź swój certyfikat **nazwa**. Typ certyfikatu wybierz **prywatnej**.

   2. W prawej części **certyfikatu** wybierz ikonę folderu. 
   Znajdź i wybierz plik certyfikatu, który chcesz przekazać. 
   Zaznacz również **grupy zasobów**, **Key Vault**, i **nazwa klucza**. 
   Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

      ![Dodawanie certyfikatu](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)

   Azure przekazywanie certyfikatu po weryfikacji wybór.

   ![Zobacz nowego certyfikatu](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie umowy z B2B](logic-apps-enterprise-integration-agreements.md)
