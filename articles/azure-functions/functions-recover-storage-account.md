---
title: Jak rozwiązywać problemy środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny.
description: Dowiedz się, jak rozwiązywać problemy z nieprawidłowym kontem magazynu.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 212f10bd33479e5a9f7244d5b2090c0324f937c2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226759"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Jak rozwiązywać problemy ze środowiskiem uruchomieniowym funkcji jest nieosiągalny "


## <a name="error-text"></a>Tekst błędu
Ten dokument jest przeznaczony do rozwiązywania problemów z następującym błędem, gdy jest wyświetlany w portalu funkcji.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

### <a name="summary"></a>Podsumowanie
Ten problem występuje, gdy nie można uruchomić środowisko uruchomieniowe usługi Azure Functions. Najbardziej typową przyczyną wystąpienia tego błędu jest aplikacja funkcji, która utraci dostęp do konta magazynu. [Przeczytaj więcej na temat wymagań dotyczących konta magazynu tutaj](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)

### <a name="troubleshooting"></a>Rozwiązywanie problemów
Przeprowadzimy cztery najczęstsze przypadki błędów, sposób identyfikowania i rozwiązywania każdego przypadku.

1. Usunięto konto magazynu
1. Usunięto ustawienia aplikacji konta magazynu
1. Nieprawidłowe poświadczenia konta magazynu
1. Konto magazynu jest niedostępne
1. Pełny limit przydziału dziennego wykonania

## <a name="storage-account-deleted"></a>Usunięto konto magazynu

Każda aplikacja funkcji wymaga konta magazynu do działania. Jeśli to konto zostanie usunięte, funkcja nie będzie działać.

### <a name="how-to-find-your-storage-account"></a>Jak znaleźć konto magazynu

Zacznij od przejrzenia nazwy konta magazynu w ustawieniach aplikacji. `AzureWebJobsStorage` lub `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` będzie zawierać nazwę konta magazynu opakowaną w ciągu połączenia. Więcej szczegółowych informacji znajduje się w [dokumentacji dotyczącej ustawień aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)

Wyszukaj swoje konto magazynu w Azure Portal, aby sprawdzić, czy jeszcze nie istnieje. Jeśli został usunięty, należy ponownie utworzyć konto magazynu i zastąpić parametry połączenia magazynu. Kod funkcji zostanie utracony i konieczne będzie ponowne wdrożenie ponownie.

## <a name="storage-account-application-settings-deleted"></a>Usunięto ustawienia aplikacji konta magazynu

W poprzednim kroku, jeśli nie masz parametrów połączenia konta magazynu, które zostały już usunięte lub nadpisane. Usuwanie ustawień aplikacji jest najczęściej wykonywane w przypadku używania miejsc wdrożenia lub skryptów Azure Resource Manager do ustawiania ustawień aplikacji.

### <a name="required-application-settings"></a>Wymagane ustawienia aplikacji

* Wymagane
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Wymagane dla funkcji planu zużycia
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Tutaj Przeczytaj informacje o tych ustawieniach aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

### <a name="guidance"></a>Wskazówki

* Nie sprawdzaj ustawień gniazda dla żadnego z tych ustawień. Podczas wymiany miejsc wdrożenia funkcja zostanie przerwana.
* Nie należy modyfikować tych ustawień w ramach zautomatyzowanych wdrożeń.
* Te ustawienia muszą być podane i ważne podczas tworzenia. Automatyczne wdrożenie, które nie zawiera tych ustawień, spowoduje, że aplikacja nie będzie funkcjonalna, nawet jeśli ustawienia zostaną dodane po tym fakcie.

## <a name="storage-account-credentials-invalid"></a>Nieprawidłowe poświadczenia konta magazynu

Jeśli klucze magazynu zostaną ponownie wygenerowane, należy zaktualizować powyższe parametry połączenia konta magazynu. [Przeczytaj więcej na temat zarządzania kluczami magazynu tutaj](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)

## <a name="storage-account-inaccessible"></a>Konto magazynu jest niedostępne

Aplikacja funkcji musi mieć możliwość uzyskania dostępu do konta magazynu. Typowe problemy z zablokowaniem dostępu do funkcji do konta magazynu są następujące:

* Aplikacje funkcji wdrożone w środowiskach App Service bez poprawnych reguł sieciowych w celu zezwolenia na ruch do i z konta magazynu
* Zapora konta magazynu jest włączona i nie jest skonfigurowana w taki sposób, aby zezwalała na ruch do i z funkcji. [Przeczytaj więcej na temat konfiguracji zapory konta magazynu tutaj](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="daily-execution-quota-full"></a>Pełny limit przydziału dziennego wykonania

W przypadku skonfigurowania dziennego przydziału wykonywania aplikacja funkcji zostanie tymczasowo wyłączona i wiele kontrolek portalu stanie się niedostępne. 

* Aby sprawdzić, zaznacz pozycję Otwórz funkcje platformy > aplikacja funkcji ustawienia w portalu. Jeśli limit przydziału zostanie przekroczony, zobaczysz następujący komunikat
    * `The Function App has reached daily usage quota and has been stopped until the next 24 hours time frame.`
* Usuń przydział i ponownie uruchom aplikację, aby rozwiązać ten problem.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy aplikacja funkcji jest z powrotem i działa, zapoznaj się z naszymi przewodnikami Szybki Start i deweloperami, aby zacząć ponownie.

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
