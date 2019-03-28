---
title: Jak rozwiązywać problemy z środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny.
description: Dowiedz się, jak rozwiązywać problemy z nieprawidłowe konto magazynu.
services: functions
documentationcenter: ''
author: alexkarcher-msft
manager: cfowler
editor: ''
ms.service: azure-functions
ms.workload: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 6057fa52cd2f1e9b9fd525723f96ab66983fb5d4
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521724"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Jak rozwiązywać problemy z "środowisko uruchomieniowe usługi functions jest nieosiągalny"


## <a name="error-text"></a>Tekst błędu
Ten dokument ma na celu rozwiązywanie problemów z powodu następującego błędu podczas wyświetlania w portalu usługi Functions.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Podsumowanie
Ten problem występuje, gdy nie można uruchomić środowisko uruchomieniowe usługi Azure Functions. Najbardziej typową przyczyną wystąpienia tego błędu jest aplikacja funkcji utraty dostępu do jego konta magazynu. [Więcej informacji na temat wymagania konta magazynu w tym miejscu](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Rozwiązywanie problemów
Omówimy cztery najbardziej typowe przypadki błędów, jak identyfikować i jak rozwiązać problem w każdym przypadku.

1. Usunięto konto magazynu
1. Usunięto ustawienia aplikacji dla konta magazynu
1. Nieprawidłowe poświadczenia konta magazynu
1. Konto magazynu jest niedostępne
1. Dzienny limit przydziału wykonywania pełnej

## <a name="storage-account-deleted"></a>Usunięto konto magazynu

Każda aplikacja funkcji wymaga konta magazynu do działania. Usunięcie tego konta funkcji nie będą działać.

### <a name="how-to-find-your-storage-account"></a>Jak znaleźć konta magazynu

Rozpocznij, wyszukując nazwę konta magazynu w ustawieniach aplikacji. Albo `AzureWebJobsStorage` lub `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` będzie zawierać nazwę otoczona parametry połączenia konta magazynu. Przeczytaj więcej szczegółowych informacji o [odwołanie ustawienia aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Wyszukiwanie konta magazynu w witrynie Azure portal, aby zobaczyć, jeśli nadal istnieje. Jeśli został on usunięty, konieczne będzie ponowne tworzenie konta magazynu i Zastąp parametry połączenia magazynu. Kod funkcji zostaną utracone i trzeba będzie ponownie wdróż go ponownie.

## <a name="storage-account-application-settings-deleted"></a>Usunięto ustawienia aplikacji dla konta magazynu

W poprzednim kroku Jeśli nie masz parametry połączenia konta magazynu były prawdopodobnie usunięte lub zastąpione. Usuwanie ustawień aplikacji zwykle odbywa się przy użyciu miejsc wdrożenia lub skryptów usługi Azure Resource Manager można skonfigurować ustawienia aplikacji.

### <a name="required-application-settings"></a>Ustawienia aplikacji

* Wymagane
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Wymagane dla funkcji Plan zużycie
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Przeczytaj o tych ustawieniach aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Wskazówki

* Nie sprawdzaj "ustawienie miejsca" dowolne z tych ustawień. Gdy zamiana miejsc wdrożenia funkcji spowoduje awarię.
* Nie należy modyfikować tych ustawień w ramach wdrożeń zautomatyzowanych.
* Te ustawienia musi być podana i prawidłowy w czasie jego tworzenia. Automatycznego wdrażania, który nie zawiera tych ustawień spowoduje współzależności funkcjonalnych aplikacji, nawet jeśli ustawienia są dodawane po fakcie.

## <a name="storage-account-credentials-invalid"></a>Nieprawidłowe poświadczenia konta magazynu

Musi zostać zaktualizowany powyżej parametry połączenia konta magazynu, jeśli zostaną ponownie wygenerowane klucze magazynu. [Więcej informacji na temat magazynu w tym miejscu zarządzania kluczami](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Konto magazynu jest niedostępne

Aplikacja funkcji musi mieć możliwość uzyskania dostępu do konta magazynu. Typowe problemy z tego bloku dostępu funkcji, do konta magazynu są:

* Funkcja wdrożonych aplikacji do środowiska usługi App Service bez reguł odpowiedniej sieci zezwalającą na ruch do i z konta magazynu
* Zapora konto magazynu jest włączona i nie są skonfigurowane i umożliwiają ruch do i z funkcji. [Dowiedz się więcej o konfigurowaniu zapory konta magazynu w tym miejscu](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Dzienny limit przydziału wykonywania pełnej

W przypadku dzienny limit przydziału wykonywania skonfigurowana aplikacja funkcji zostanie tymczasowo wyłączona i wiele formantów portalu staną się niedostępne. 

* Aby sprawdzić, sprawdź Otwórz funkcje platformy > Ustawienia aplikacji funkcji w portalu. Zostanie wyświetlony następujący komunikat, jeśli Przekroczono limit przydziału
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Usuń limit przydziału, a następnie uruchom ponownie swoją aplikację, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy aplikacja funkcji jest Wstecz i operacyjnych, zapoznaj się z naszych przewodników Szybki Start i deweloperów odwołania do uruchomienia i uruchomić ponownie!

* [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-azure-function.md)  
  Od razu utwórz swoją pierwszą funkcję przy użyciu opcji szybkiego startu usługi Azure Functions. 
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dalsze informacje techniczne na temat środowiska uruchomieniowego usługi Azure Functions, a także dokumentacja dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.
* [Testowanie usługi Azure Functions](functions-test-a-function.md)  
  Opis różnych narzędzi i technik testowania funkcji.
* [Jak skalować usługę Azure Functions](functions-scale.md)  
  Omówienie planów usług dostępnych w środowisku Azure Functions, w tym planu hostingowego zużycia, oraz sposobu wybierania właściwego planu. 
* [Dowiedz się więcej o usłudze Azure App Service](../app-service/overview.md)  
  Środowisko Azure Functions korzysta z platformy Azure App Service na potrzeby funkcji podstawowych, takich jak wdrożenia, zmienne środowiskowe i diagnostyka. 
