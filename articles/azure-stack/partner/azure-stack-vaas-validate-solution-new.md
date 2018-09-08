---
title: Weryfikowanie nowych rozwiązań usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzanie poprawności nowego rozwiązania usługi Azure Stack z weryfikacją jako usługa.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1e908a8cf5576ce3bc3d58d1ef6f29d596ebc58b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158181"
---
# <a name="validate-a-new-azure-stack-solution"></a>Weryfikowanie nowych rozwiązań usługi Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dowiedz się, jak umożliwia przepływ pracy weryfikacji rozwiązania do certyfikowania nowych rozwiązań usługi Azure Stack.

Rozwiązanie usługi Azure Stack jest sprzętu zestawienie komponentów (BoM) został wspólnie uzgodniony z firmą Microsoft, który został przekazany logo systemu Windows Server, wymagania dotyczące certyfikacji. Można również użyć przepływu pracy weryfikacji rozwiązania, gdy nastąpiła zmiana na sprzęt BoM, które spowodują, że rozwiązanie jest uważany za *nowe*. W przypadku pytań dotyczących co powodowało **nowe** lub **wystawiły** rozwiązania skontaktować się z [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Aby przeprowadzić certyfikację rozwiązania, uruchom dwa razy przepływ pracy. Uruchomić go jeden raz dla *minimalny zestaw* obsługiwana konfiguracja. Uruchom w nim po raz drugi dla *maksymalna* konfiguracji. Certyfikacja potwierdzająca rozwiązania firmy Microsoft, jeśli obie konfiguracje pomyślnie przejść wszystkie testy.

Ten przewodnik Szybki Start ułatwia pracę w procesie dodanie swojego rozwiązania i wykonywania testów.

## <a name="add-a-new-solution"></a>Dodaj nowe rozwiązanie

1. Zaloguj się do [portalu weryfikacji](https://azurestackvalidation.com).
2. Wybierz **nowe rozwiązanie**.
3. Wprowadź nazwę dla rozwiązania, a następnie wybierz **Zapisz**.

## <a name="create-a-solution-validation-workflow"></a>Tworzenie rozwiązań przepływu pracy weryfikacji

1. Wybierz nazwę rozwiązania.
2. Wybierz **Zarządzaj** na **walidacji rozwiązania** kafelka.

    ![Sprawdzanie poprawności rozwiązania](media/image2.png)

## <a name="create-a-solution-workflow"></a>Tworzenie rozwiązań przepływu pracy

1. Wybierz **nowe rozwiązanie weryfikacji**.
2. Wpisz nazwę sprawdzania poprawności.
3. Wybierz **Minimum** lub **maksymalna**.  
    - **Minimum**  
    To rozwiązanie jest skonfigurowany z obsługiwanych minimalna liczba węzłów.  
    - **Maksymalna**  
    To rozwiązanie jest skonfigurowany z maksymalną obsługiwaną liczbę węzłów.
4. Wybierz **przekazywanie** , a następnie dodać plik konfiguracji wdrożenia. Jest to krok opcjonalny. Można również dodać parametry testu, wykonując kroki opisane w następnej sekcji.

    > [!note]  
    > Można utworzyć pliku konfiguracji, dodając parametry za pomocą parametrów środowiska wspólne sekcje parametrów testu w interfejsie. Możesz pobrać plik generowane przez usługę z wdrożenia usługi Azure Stack, sprawdzania poprawności. Aby uzyskać instrukcje, zobacz [wspólnych parametrów przepływów pracy dla usługi Azure Stack weryfikacji jako usługa](azure-stack-vaas-parameters.md).

5. Dodaj parametry środowiska. Aby uzyskać więcej informacji, zobacz [Dodaj parametry środowiska](#add-environmental-parameters).
6. Dodaj wspólne parametry testu. Aby uzyskać więcej informacji, zobacz [dodawać typowych parametrów testu](#add-common-test-parameters).

    W zależności od definicji testu test może wymagać wprowadzenia wartości niezależnie od typowych parametrów lub mogą zezwolić na zastąpienie typowe wartości parametru.

7. Kliknij przycisk **przesyłania** do zaplanowania testów.

## <a name="add-environmental-parameters"></a>Dodaj parametry środowiska

Dodaj następujące parametry środowiska:

| Informacje o przebiegu testu | Wymagane | Opis |
| --- | --- | --- | --- |
| Usługa Azure Stack kompilacji | Wymagane | Azure Stack kompilacji, wartość liczbowa (na przykład 20170501.1) musi być prawidłową usługi Azure Stack tworzenie numer lub wersji, na przykład 1.0.170330.9 |
| Identyfikator dzierżawy | Wymagane | Identyfikator dzierżawy usługi Active Directory Musi to być identyfikator GUID (na przykład ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| Region | Wymagane | Regionu wdrożenia w usłudze Azure Stack |
| Punkt końcowy dzierżawy Resource Manager | Wymagane | Punkt końcowy dla operacji dzierżawy usługi Azure Resource Manager (na przykład https://management.loc-ext.domain.com) |
| Punkt końcowy administratora Resource Manager | Niewymagane | Punkt końcowy dla operacji dzierżawy usługi Azure Resource Manager (na przykład https://management.loc-ext.domain.com) |
| Nazwa FQDN zewnętrznej | Niewymagane | Zewnętrzne w pełni kwalifikowana nazwa domeny używana jako sufiks dla punktów końcowych. (na przykład local.azurestack.external lub redmond.contoso.com) |
| Liczba węzłów | Wymagane | Liczba węzłów w rozwiązaniu. |

## <a name="add-common-test-parameters"></a>Dodawanie wspólnych parametrów testu

Dodaj następujące typowe parametry testu:

| Informacje o przebiegu testu | Wymagane | Opis |
| --- | --- | --- |
| Nazwa użytkownika dzierżawy | Wymagane | Dzierżawy nazwy użytkownika (na przykład tenant@contoso.onmicrosoft.com) |
| Hasło dzierżawy | Wymagane | Hasło dla dzierżawy. |
| Nazwa użytkownika administratora usługi | Niewymagane | Dzierżawy nazwy użytkownika (na przykład tenant@contoso.onmicrosoft.com) |
| Hasło administratora usługi | Niewymagane | Nazwa użytkownika administratora usługi (na przykład serviceadmin@contoso.onmicrosoft.com) |
| Nazwa użytkownika administratora chmury | Niewymagane | Usługa Azure Stack konta administratora domeny (na przykład contoso\cloudadmin) |
| Hasło administratora chmury | Niewymagane | |
|  Parametry połączenia diagnostyki | Niewymagane | Identyfikator URI sygnatury dostępu Współdzielonego konta usługi Azure Storage do diagnostyki, które dzienniki będą kopiowane podczas wykonywania testów. Zobacz [Tworzenie usługi Azure storage blob do przechowywania dzienników](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs). <br><br>Wartość **parametry połączenia diagnostyki** typowego parametru będą przechowywane przez usługę i podano w momencie harmonogramu, aby wszystkie testy w przepływie pracy, które korzystają z tego parametru. W przypadku adresu URL sygnatury dostępu Współdzielonego w ciągu 30 dni wygaśnięcia, użytkownik jest monitowany dla nowego adresu URL sygnatury dostępu Współdzielonego na stronie typowych parametrów. |
| Tag — nazwa | Niewymagane |  Opisowe znaczniki mogą być wprowadzane do etykiety przepływu pracy. Jest to nazwa tagu. |
| Tag — wartości | Niewymagane | Opisowe znaczniki mogą być wprowadzane do etykiety przepływu pracy. Jest to wartość tagu. |

## <a name="next-steps"></a>Kolejne kroki

- [Zmienić termin egzaminu lub anulować testu](azure-stack-vaas-monitor-test.md#reschedule-a-test)
- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).