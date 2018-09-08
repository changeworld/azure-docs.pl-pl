---
title: Użyj sprawdzania poprawności jako usługa dla usługi Azure Stack portal do zaplanowania Twojego pierwszego testu | Dokumentacja firmy Microsoft
description: Aby zaplanować pierwszy test, należy użyć weryfikacji jako usługa dla portalu Azure Stack.
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
ms.openlocfilehash: 8ff78f46283d3ee4baa7310aa56bcdabde85f2cc
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159626"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>Szybki Start: Użyj weryfikacji jako portal usługi do zaplanowania Twojego pierwszego testu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Dowiedz się, jak zaplanować pierwszy test z weryfikacji jako portal usługi (VaaS), aby sprawdzić sprzętu. Agent lokalny należy wdrożyć rozwiązania usługi Azure Stack, weryfikowany przed uruchomieniem testów sprawdzania poprawności.

W tym przewodniku Szybki Start należy Dodaj rozwiązanie i uruchom testy.

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem tego przewodnika Szybki Start, musisz mieć:
 - Sprawdzanie poprawności jako konto usługi. Aby uzyskać instrukcje, zobacz [Konfigurowanie walidacji jako konto usługi](azure-stack-vaas-set-up-account.md).  
- Jeśli agent lokalny jest zainstalowany w systemie. Aby uzyskać instrukcje, zobacz [wdrażanie lokalnych maszyn wirtualnych agenta i testowania](azure-stack-vaas-test-vm.md).

## <a name="add-a-new-solution"></a>Dodaj nowe rozwiązanie

1. Zaloguj się do [portalu weryfikacji](https://azurestackvalidation.com).

    ![Zaloguj się do portalu weryfikacji](media/vaas_portalsignin.png)  

2. Wybierz **nowe rozwiązanie**.
3. Wprowadź nazwę dla rozwiązania, a następnie wybierz pozycję **Zapisz**.

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
4. Dodaj parametry środowiska. Aby uzyskać więcej informacji, zobacz [Dodaj parametry środowiska](#add-environmental-parameters).
5. Dodaj wspólne parametry testu. Aby uzyskać więcej informacji, zobacz [dodawać typowych parametrów testu](#add-common-test-parameters).

    W zależności od definicji testu test może wymagać wprowadzenia wartości niezależnie od typowych parametrów lub mogą zezwolić na zastąpienie typowe wartości parametru.
6. Kliknij przycisk **przesyłania** do zaplanowania testów.

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

- [Weryfikowanie nowych rozwiązań usługi Azure Stack](azure-stack-vaas-validate-solution-new.md)  
- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).
