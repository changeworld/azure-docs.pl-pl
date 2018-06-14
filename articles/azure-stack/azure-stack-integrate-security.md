---
title: Integracja Azure datacenter stosu - zabezpieczeń
description: Dowiedz się, jak zintegrować zabezpieczeń stosu Azure z zabezpieczeń Centrum danych
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 8ce9045a3e4fd12d61e9b1600ee98880762bc544
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2018
ms.locfileid: "29734431"
---
# <a name="azure-stack-datacenter-integration---security"></a>Integracja Azure datacenter stosu - zabezpieczeń
Azure stos został zaprojektowany i zbudowany z myślą o bezpieczeństwie. Azure stosu jest systemem zablokowany, więc instalacji agenta zabezpieczeń oprogramowania nie jest obsługiwany.

W tym artykule opisano, jak zintegrować funkcje zabezpieczeń stosu Azure z rozwiązań zabezpieczeń już wdrożone w centrum danych.

## <a name="security-logs"></a>Dzienniki zabezpieczeń

Azure stosu zbiera systemu operacyjnego i zdarzeń zabezpieczeń dla ról infrastruktury i węzły jednostki skali co dwie minuty. Dzienniki są przechowywane w kontenerów obiektów blob na koncie magazynu.

Istnieje jedno konto magazynu dla każdej roli infrastruktury i jedno konto magazynu ogólne dla wszystkich zdarzeń typowego systemu operacyjnego.

Dostawca zasobów kondycji można wywołać za pomocą protokołu REST do pobierania adresu URL do kontenera obiektów blob. Rozwiązania innych firm zabezpieczeń można użyć interfejsu API i przechowywania konta do pobierania zdarzeń do przetwarzania.

### <a name="use-azure-storage-explorer-to-view-events"></a>Użyj Eksploratora usługi Storage platformy Azure, aby wyświetlić zdarzenia

Można pobrać zdarzenia zebrane przez stos Azure przy użyciu narzędzia Eksploratora usługi Storage platformy Azure. Możesz pobrać z Eksploratora usługi Storage Azure [http://storageexplorer.com](http://storageexplorer.com).

Poniższa procedura jest przykładem, używanych do konfigurowania Eksploratora usługi Storage Azure dla stosu Azure:

1. Zaloguj się do portalu administratora stosu Azure jako operatora.
2. Przeglądaj **kont magazynu** i poszukaj **frphealthaccount**. **Frphealthaccount** konto jest kontem magazynu ogólnego używane do przechowywania wszystkich zdarzeń systemu operacyjnego.

   ![Konta magazynu](media/azure-stack-integrate-security/storage-accounts.png)

3. Wybierz **frphealthaccount**, następnie kliknij przycisk **klucze dostępu**.

   ![Klawisze dostępu](media/azure-stack-integrate-security/access-keys.png)

4. Skopiuj klucz dostępu do Schowka.
5. Otwórz Eksploratora usługi Storage platformy Azure.
6. Na **Edytuj** menu, wybierz opcję **stosu Azure docelowej**.
7. Wybierz **Dodaj konto**, a następnie wybierz **użyć nazwy konta magazynu i klucza**.

   ![Połączenia magazynu](media/azure-stack-integrate-security/connect-storage.png)

8. Kliknij przycisk **Dalej**.
9. Na **Dołącz zewnętrzną usługę Storage** strony:

   a. Wpisz nazwę konta **frphealthaccount**.

   b. Wklej klucz dostępu do konta magazynu.

   c. W obszarze **domeny punkty końcowe magazynu**, wybierz pozycję **innych**i określ punkt końcowy magazynu **[Region]. [ Nazwa_domeny]**.

   d. Wybierz **Użyj protokołu HTTP** pole wyboru.

   ![Dołącz magazynu zewnętrznego](media/azure-stack-integrate-security/attach-storage.png)

10. Kliknij przycisk **dalej**, zapoznaj się z podsumowaniem i **Zakończ** kreatora.
11. Teraz można przeglądać kontenery poszczególnych obiektów blob i pobrać zdarzenia.

   ![Przeglądaj obiekty BLOB](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Użyj języków programowania dostępu zdarzeń

Do uzyskania dostępu do konta magazynu, można użyć różnych języków programowania. Wybierz przykład zgodnym z językiem za pomocą następującej dokumentacji:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Przeprowadzanie inspekcji dostępu do urządzenia

Wszystkie urządzenia fizycznego w stosie Azure obsługuje TACACS lub usługi RADIUS. Obejmuje to dostęp do kontrolera zarządzania płytą główną (BMC) i przełączniki sieciowe.

Azure rozwiązań stosu nie są dostarczane z usługi RADIUS lub TACACS wbudowane. Jednak zostały zatwierdzone rozwiązania do obsługi istniejących rozwiązań usługi RADIUS lub TACACS dostępnych na rynku.

Promień tylko MSCHAPv2 została zweryfikowana. Reprezentuje implementację najbardziej bezpieczne korzystanie z usługi RADIUS.
Zapoznaj się z dostawcą sprzętu OEM, aby włączyć RADIUS lub TACAS urządzenia dołączone do rozwiązania Azure stosu.

## <a name="syslog"></a>Dziennik systemu

Wszystkie urządzenia fizycznego w stosie Azure może wysyłać komunikaty dziennika systemowego. Azure rozwiązań stosu nie są dostarczane z serwera Syslog. Jednak zostały zatwierdzone rozwiązania do obsługi wysyłanie wiadomości do istniejących rozwiązań Syslog na rynku.

Adres docelowy Syslog jest opcjonalny parametr zbierane na potrzeby wdrożenia, ale można jej również dodać po wdrożeniu.

## <a name="next-steps"></a>Kolejne kroki

[Obsługa zasad](azure-stack-servicing-policy.md)
