---
title: 'Błąd rozwiązywania problemów: Środowisko wykonawcze funkcji platformy Azure jest nieosiągalne'
description: Dowiedz się, jak rozwiązać problem z nieprawidłowym kontem magazynu.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063785"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Rozwiązywanie problemów z błędem: "Środowisko uruchomieniowe funkcji platformy Azure jest nieosiągalne"

Ten artykuł ułatwia rozwiązywanie problemów z następującym ciągiem błędów wyświetlanym w witrynie Azure Portal:

> "Błąd: Środowisko wykonawcze funkcji platformy Azure jest nieosiągalne. Kliknij tutaj, aby uzyskać szczegółowe informacje na temat konfiguracji pamięci masowej."

Ten problem występuje, gdy nie można uruchomić środowiska wykonawczego funkcji platformy Azure. Najczęstszą przyczyną problemu jest to, że aplikacja funkcji utraciła dostęp do swojego konta magazynu. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące konta magazynu](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).

W dalszej części tego artykułu pomaga rozwiązać następujące przyczyny tego błędu, w tym jak zidentyfikować i rozwiązać każdy przypadek.

## <a name="storage-account-was-deleted"></a>Konto magazynu zostało usunięte

Każda aplikacja funkcji wymaga konta magazynu do działania. Jeśli to konto zostanie usunięte, funkcja nie będzie działać.

Zacznij od wyszukując nazwę konta magazynu w ustawieniach aplikacji. Albo `AzureWebJobsStorage` `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` zawiera nazwę konta magazynu zawiniętego w parametry połączenia. Aby uzyskać więcej informacji, zobacz [Odwołanie do ustawień aplikacji dla usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage).

Wyszukaj swoje konto magazynu w witrynie Azure portal, aby sprawdzić, czy nadal istnieje. Jeśli został usunięty, ponownie utwórz konto magazynu i zastąp parametry połączenia magazynu. Kod funkcji zostanie utracony i należy go ponownie wdrożyć.

## <a name="storage-account-application-settings-were-deleted"></a>Ustawienia aplikacji konta magazynu zostały usunięte

W poprzednim kroku, jeśli nie można znaleźć parametry połączenia konta magazynu, prawdopodobnie został usunięty lub zastąpiony. Usuwanie ustawień aplikacji najczęściej ma miejsce, gdy używasz gniazd wdrażania lub skryptów usługi Azure Resource Manager, aby ustawić ustawienia aplikacji.

### <a name="required-application-settings"></a>Wymagane ustawienia aplikacji

* Wymagane:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* Wymagane dla funkcji planu zużycia:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

Aby uzyskać więcej informacji, zobacz [Odwołanie do ustawień aplikacji dla usługi Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-app-settings).

### <a name="guidance"></a>Wskazówki

* Nie sprawdzaj "ustawienia gniazda" dla żadnego z tych ustawień. W przypadku wymiany gniazd wdrażania aplikacja funkcji zostanie przerwana.
* Nie modyfikuj tych ustawień w ramach automatycznych wdrożeń.
* Te ustawienia muszą być podane i ważne w czasie tworzenia. Automatyczne wdrożenie, które nie zawiera tych ustawień, powoduje, że aplikacja funkcji, która nie będzie działać, nawet jeśli ustawienia zostaną dodane później.

## <a name="storage-account-credentials-are-invalid"></a>Poświadczenia konta magazynu są nieprawidłowe

Wcześniej omówione parametry połączenia konta magazynu muszą zostać zaktualizowane, jeśli ponownie wygenerujesz klucze magazynu. Aby uzyskać więcej informacji na temat zarządzania kluczami magazynu, zobacz [Tworzenie konta usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account).

## <a name="storage-account-is-inaccessible"></a>Konto magazynu jest niedostępne

Aplikacja funkcji musi mieć dostęp do konta magazynu. Typowe problemy, które blokują dostęp aplikacji funkcji do konta magazynu to:

* Aplikacja funkcji jest wdrażana w środowisku usługi App Service bez odpowiednich reguł sieciowych, aby umożliwić ruch do i z konta magazynu.

* Zapora konta magazynu jest włączona i nie jest skonfigurowana tak, aby zezwalała na ruch do i z funkcji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie zapór i sieci wirtualnych usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="daily-execution-quota-is-full"></a>Dzienny przydział wykonania jest pełny

Jeśli masz dzienny przydział wykonania skonfigurowany, aplikacja funkcji jest tymczasowo wyłączona, co powoduje, że wiele formantów portalu stają się niedostępne. 

Aby zweryfikować przydział w [witrynie Azure portal,](https://portal.azure.com)wybierz**pozycję Ustawienia aplikacji funkcji funkcji** **platformy** > w aplikacji funkcji. Jeśli przekroczyłeś ustawiony **przydział dziennego użycia,** zostanie wyświetlony następujący komunikat:

  > "Aplikacja funkcji osiągnęła dzienny przydział użycia i została zatrzymana do następnego 24 godzin."

Aby rozwiązać ten problem, usuń lub zwiększ dzienny przydział, a następnie uruchom ponownie aplikację. W przeciwnym razie wykonanie aplikacji jest blokowane do następnego dnia.

## <a name="app-is-behind-a-firewall"></a>Aplikacja znajduje się za zaporą

Środowisko wykonawcze funkcji może być nieosiągalne z jednego z następujących powodów:

* Aplikacja funkcji jest hostowana w [środowisku usługi app service z równoważeniem obciążenia wewnętrznego](../app-service/environment/create-ilb-ase.md) i jest skonfigurowana do blokowania przychodzącego ruchu internetowego.

* Aplikacja funkcji ma [ograniczenia przychodzącego adresu IP,](functions-networking-options.md#inbound-ip-restrictions) które są skonfigurowane do blokowania dostępu do Internetu. 

Portal Azure sprawia, że wywołania bezpośrednio do uruchomionej aplikacji, aby pobrać listę funkcji i sprawia, że wywołania HTTP do punktu końcowego Kudu. Ustawienia na poziomie platformy w **zakładce Funkcje platformy** są nadal dostępne.

Aby zweryfikować konfigurację środowiska usługi App Service:
1. Przejdź do sieciowej grupy zabezpieczeń (NSG) podsieci, w której znajduje się środowisko usługi app service.
1. Sprawdź poprawność reguł ruchu przychodzącego, aby zezwolić na ruch pochodzący z publicznego adresu IP komputera, na którym uzyskujesz dostęp do aplikacji. 
   
Portalu można również użyć z komputera podłączonego do sieci wirtualnej z uruchomioną aplikacją lub z maszyną wirtualną działającą w sieci wirtualnej. 

Aby uzyskać więcej informacji na temat konfiguracji reguł ruchu [przychodzącego,](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)zobacz sekcję "Sieciowe grupy zabezpieczeń" zagadnienia dotyczące sieci dla środowiska usługi app service .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o monitorowaniu aplikacji funkcyjnych:

> [!div class="nextstepaction"]
> [Monitorowanie usługi Azure Functions](functions-monitoring.md)
