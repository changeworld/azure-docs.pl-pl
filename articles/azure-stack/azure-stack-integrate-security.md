---
title: Stos datacenter Integracja z platformą Azure — zabezpieczenia
description: Dowiedz się, jak zintegrować zabezpieczeń usługi Azure Stack przy użyciu zabezpieczeń Centrum danych
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 9f356b814ac1ac6ca8b6d6efe7cb9f5d9ed66270
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442479"
---
# <a name="azure-stack-datacenter-integration---security"></a>Stos datacenter Integracja z platformą Azure — zabezpieczenia
Usługa Azure Stack został zaprojektowany i zbudowany z myślą o bezpieczeństwie. Usługa Azure Stack jest system zablokowany, dzięki czemu instalacja agenta zabezpieczeń oprogramowania nie jest obsługiwana.

Ten artykuł ułatwia integrację funkcji zabezpieczeń usługi Azure Stack z rozwiązaniami zabezpieczeń już wdrożone w Twoim centrum danych.

## <a name="security-logs"></a>Dzienniki zabezpieczeń

Usługa Azure Stack umożliwia zbieranie informacji o system operacyjny i zdarzeń zabezpieczeń pod kątem ról infrastruktury i węzły jednostki skalowania co dwie minuty. Dzienniki są przechowywane w kontenerach obiektów blob konta magazynu.

Istnieje jedno konto magazynu dla infrastruktury i jedno konto magazynu ogólnego dla wszystkich zdarzeń rozmiar typowego systemu operacyjnego.

Dostawca zasobów kondycji mogą być wywoływane za pośrednictwem protokołu REST, aby pobrać adres URL do kontenera obiektów blob. Rozwiązania w zakresie zabezpieczeń innych firm, można użyć konta interfejsu API i magazynu do pobierania zdarzeń do przetworzenia.

### <a name="use-azure-storage-explorer-to-view-events"></a>Użyj Eksploratora usługi Azure Storage, aby wyświetlić zdarzenia

Możesz pobrać zdarzenia zebrane przez usługę Azure Stack przy użyciu narzędzia Eksploratora usługi Azure Storage. Możesz pobrać Eksplorator usługi Azure Storage z [ http://storageexplorer.com ](http://storageexplorer.com).

Poniższa procedura to przykład służących do konfigurowania Eksploratora usługi Azure Storage dla usługi Azure Stack:

1. Zaloguj się do portalu administratora usługi Azure Stack jako operator.
1. Przeglądaj **kont magazynu** i poszukaj **frphealthaccount**. **Frphealthaccount** konto jest używane do przechowywania wszystkich zdarzeń systemu operacyjnego konta magazynu ogólnego.

   ![Konta magazynu](media/azure-stack-integrate-security/storage-accounts.png)

1. Wybierz **frphealthaccount**, następnie kliknij przycisk **klucze dostępu**.

   ![Klawisze dostępu](media/azure-stack-integrate-security/access-keys.png)

1. Skopiuj klucz dostępu do Schowka.
1. Otwórz Eksplorator usługi Azure Storage.
1. Na **Edytuj** menu, wybierz opcję **docelowej usługi Azure Stack**.
1. Wybierz **Dodaj konto**, a następnie wybierz pozycję **Użyj klucza i nazwy konta magazynu**.

   ![Połączenia magazynu](media/azure-stack-integrate-security/connect-storage.png)

1. Kliknij przycisk **Dalej**.
1. Na **dołączanie zewnętrznej usługi Storage** strony:

   a. Wpisz nazwę konta **frphealthaccount**.

   b. Wklej klucz dostępu konta magazynu.

   c. W obszarze **domena punktów końcowych magazynu**, wybierz opcję **innych**, a następnie określ punkt końcowy magazynu **[Region]. [ Nazwa_domeny]**.

   d. Wybierz **Użyj protokołu HTTP** pole wyboru.

   ![Dołącz magazyn zewnętrzny](media/azure-stack-integrate-security/attach-storage.png)

1. Kliknij przycisk **dalej**, Przejrzyj podsumowanie, a **Zakończ** kreatora.
1. Możesz teraz przeglądać kontenery poszczególnych obiektów blob i pobrać zdarzenia.

   ![Przeglądaj obiekty BLOB](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Użyj do zdarzenia dostępu do języków programowania

Dostęp do konta magazynu, można użyć różnych językach programowania. Aby wybrać przykładowi, który jest zgodny z językiem, korzystając z poniższej dokumentacji:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Przeprowadzanie inspekcji dostępu do urządzenia

Wszystkie fizyczne urządzenia w usłudze Azure Stack obsługuje TACACS lub serwera RADIUS. Obejmuje to dostęp do kontrolera zarządzania płytą główną (BMC) i przełącznikami sieciowymi.

Rozwiązania platformy Azure Stack nie są dostarczane za pomocą usługi RADIUS lub TACACS wbudowane. Jednak te rozwiązania zostały zweryfikowane aby obsługiwały korzystanie z istniejących rozwiązań usługi RADIUS lub TACACS dostępnych na rynku.

Promień tylko MSCHAPv2 została zweryfikowana. Reprezentuje implementację najbardziej bezpieczne korzystanie z usługi RADIUS.
Zapoznaj się z dostawcą sprzętu producenta OEM w celu włączenia TACAS ani usługi RADIUS na urządzeniach dołączonych do rozwiązania usługi Azure Stack.

## <a name="syslog"></a>Dziennik systemu

Wszystkie fizyczne urządzenia w usłudze Azure Stack można wysłać komunikaty dziennika systemowego. Rozwiązania platformy Azure Stack nie są dostarczane z serwera Syslog. Jednak te rozwiązania zostały zweryfikowane umożliwiają wysyłanie wiadomości do istniejących rozwiązań usługi Syslog na rynku.

Adres docelowy Syslog jest opcjonalny parametr zbierane na potrzeby wdrożenia, ale mogą być również dodawane po wdrożeniu.

## <a name="next-steps"></a>Kolejne kroki

[Obsługa zasad](azure-stack-servicing-policy.md)
