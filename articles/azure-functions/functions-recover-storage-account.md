---
title: Jak rozwiązywać problemy środowisko uruchomieniowe usługi Azure Functions jest nieosiągalny.
description: Dowiedz się, jak rozwiązywać problemy z nieprawidłowym kontem magazynu.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963890"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>Jak rozwiązywać problemy ze środowiskiem uruchomieniowym funkcji jest nieosiągalny "

Ten artykuł ma na celu rozwiązywanie problemów z komunikatem o błędzie "środowisko uruchomieniowe funkcji jest nieosiągalny", gdy jest wyświetlany w Azure Portal. W przypadku wystąpienia tego błędu w portalu zostanie wyświetlony następujący ciąg błędu.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

Dzieje się tak, gdy nie można uruchomić środowisko uruchomieniowe usługi Azure Functions. Najbardziej typową przyczyną wystąpienia tego błędu jest aplikacja funkcji, która utraci dostęp do konta magazynu. Aby dowiedzieć się więcej, zobacz [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).

Pozostała część tego artykułu pomaga rozwiązywać następujące przyczyny tego błędu, w tym informacje dotyczące identyfikowania i rozwiązywania każdego przypadku.

+ [Usunięto konto magazynu](#storage-account-deleted)
+ [Usunięto ustawienia aplikacji konta magazynu](#storage-account-application-settings-deleted)
+ [Poświadczenia konta magazynu są nieprawidłowe](#storage-account-credentials-invalid)
+ [Konto magazynu jest niedostępne](#storage-account-inaccessible)
+ [Przekroczono limit przydziału dziennych wykonań](#daily-execution-quota-full)
+ [Twoja aplikacja znajduje się za zaporą](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>Usunięto konto magazynu

Każda aplikacja funkcji wymaga konta magazynu do działania. Jeśli to konto zostanie usunięte, funkcja nie będzie działać.

### <a name="how-to-find-your-storage-account"></a>Jak znaleźć konto magazynu

Zacznij od przejrzenia nazwy konta magazynu w ustawieniach aplikacji. `AzureWebJobsStorage` lub `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` będzie zawierać nazwę konta magazynu opakowaną w ciągu połączenia. Więcej szczegółowych informacji znajduje się w [dokumentacji dotyczącej ustawień aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Wyszukaj swoje konto magazynu w Azure Portal, aby sprawdzić, czy jeszcze nie istnieje. Jeśli został usunięty, należy ponownie utworzyć konto magazynu i zastąpić parametry połączenia magazynu. Kod funkcji zostanie utracony i konieczne będzie ponowne wdrożenie ponownie.

## <a name="storage-account-application-settings-deleted"></a>Usunięto ustawienia aplikacji konta magazynu

W poprzednim kroku, jeśli nie masz parametrów połączenia konta magazynu, prawdopodobnie został on usunięty lub nadpisany. Usuwanie ustawień aplikacji jest najczęściej wykonywane w przypadku używania miejsc wdrożenia lub skryptów Azure Resource Manager do ustawiania ustawień aplikacji.

### <a name="required-application-settings"></a>Wymagane ustawienia aplikacji

* Wymagane
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Wymagane dla funkcji planu zużycia
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[Tutaj Przeczytaj informacje o tych ustawieniach aplikacji](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Wskazówka

* Nie sprawdzaj ustawień gniazda dla żadnego z tych ustawień. Podczas wymiany miejsc wdrożenia podział aplikacji funkcji.
* Nie należy modyfikować tych ustawień w ramach zautomatyzowanych wdrożeń.
* Te ustawienia muszą być podane i ważne podczas tworzenia. Automatyczne wdrożenie, które nie zawiera tych ustawień, powoduje, że aplikacja funkcji nie zostanie uruchomiona, nawet jeśli ustawienia zostaną dodane później.

## <a name="storage-account-credentials-invalid"></a>Nieprawidłowe poświadczenia konta magazynu

Jeśli klucze magazynu zostaną ponownie wygenerowane, należy zaktualizować powyższe parametry połączenia konta magazynu. [Przeczytaj więcej na temat zarządzania kluczami magazynu tutaj](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-inaccessible"></a>Konto magazynu jest niedostępne

Aplikacja funkcji musi mieć dostęp do konta magazynu. Typowe problemy z zablokowaniem dostępu do funkcji do konta magazynu są następujące:

+ aplikacje funkcji wdrożone w środowiskach App Service (ASE) bez poprawnych reguł sieci, aby umożliwić ruch do i z konta magazynu.

+ Zapora konta magazynu jest włączona i nie jest skonfigurowana w taki sposób, aby zezwalała na ruch do i z funkcji. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](../storage/common/storage-network-security.md).

## <a name="daily-execution-quota-full"></a>Pełny limit przydziału dziennego wykonania

W przypadku skonfigurowania dziennego przydziału wykonywania aplikacja funkcji jest tymczasowo wyłączona, co spowoduje, że wiele kontrolek portalu stanie się niedostępna. 

+ Aby sprawdzić w [Azure Portal](https://portal.azure.com), Otwórz pozycję **funkcje platformy** > **aplikacja funkcji ustawienia** w aplikacji funkcji. Gdy ustawisz **dzienny limit przydziału użycia** , zobaczysz następujący komunikat:

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ Aby rozwiązać ten problem, Usuń lub Zwiększ dzienny limit przydziału i ponownie uruchom aplikację. W przeciwnym razie wykonywanie aplikacji jest blokowane do następnego dnia.

## <a name="app-is-behind-a-firewall"></a>Aplikacja znajduje się za zaporą

Środowisko uruchomieniowe funkcji będzie nieosiągalne, jeśli aplikacja funkcji jest hostowana w ramach [wewnętrznego równoważenia obciążenia App Service Environment](../app-service/environment/create-ilb-ase.md) i jest skonfigurowana do blokowania przychodzącego ruchu internetowego lub ma [ograniczenia przychodzące IP](functions-networking-options.md#inbound-ip-restrictions) skonfigurowane do blokowania dostępu do Internetu. Azure Portal wykonuje wywołania bezpośrednio do uruchomionej aplikacji w celu pobrania listy funkcji, a także nawiązuje wywołania HTTP do punktu końcowego KUDU. Ustawienia poziomu platformy na karcie `Platform Features` będą nadal dostępne.

Aby sprawdzić konfigurację środowiska ASE, przejdź do sieciowej grupy zabezpieczeń podsieci, w której znajduje się środowisko ASE, i Sprawdź reguły ruchu przychodzącego, aby zezwolić na ruch pochodzący z publicznego adresu IP komputera, na którym uzyskujesz dostęp do aplikacji. Portalu można także użyć z komputera połączonego z siecią wirtualną, na której działa aplikacja lub maszyna wirtualna działająca w sieci wirtualnej. [Więcej informacji na temat konfiguracji reguły ruchu przychodzącego można znaleźć tutaj](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o monitorowaniu aplikacji funkcji:

> [!div class="nextstepaction"]
> [Monitoruj Azure Functions](functions-monitoring.md)
