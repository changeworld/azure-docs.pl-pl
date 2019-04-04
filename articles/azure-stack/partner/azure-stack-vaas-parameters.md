---
title: Wspólne parametry przepływów pracy w usługi Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
description: Wspólne parametry przepływów pracy dla usługi Azure Stack weryfikacji jako usługa
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 9513b552ce6bfd525077270b90d3d10e31c015c5
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484966"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Wspólne parametry przepływów pracy dla usługi Azure Stack weryfikacji jako usługa

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Wspólne parametry zawierają wartości, takie jak zmienne środowiskowe i użytkownika poświadczeń wymaganych przez wszystkie testy sprawdzania poprawności jako usługa (VaaS). Te wartości są definiowane na poziomie przepływu pracy, podczas tworzenia lub zmienić przepływ pracy. Podczas planowania testów, te wartości są przekazywane jako parametry do każdego testu, w ramach przepływu pracy.

> [!NOTE]
> Każdy test definiuje swój własny zestaw parametrów. W czasie harmonogramu test może wymagać wprowadzenia wartości niezależnie od typowych parametrów lub mogą zezwolić na zastąpienie typowe wartości parametru.

## <a name="environment-parameters"></a>Parametry środowiska

Parametry środowiska opisują środowiska usługi Azure Stack w ramach testu. Te wartości musi być podana przez generowanie i przekazywanie pliku usługi Azure Stack sygnatury informacji w ramach określonego wystąpienia, które testujesz.

> [!NOTE]
> W przepływach pracy oficjalne sprawdzania poprawności parametrów środowiska nie można zmodyfikować po utworzeniu przepływu pracy.

### <a name="generate-the-stamp-information-file"></a>Generowanie pliku informacji sygnatury

1. Zaloguj się do Menedżer DVM lub dowolnym komputerze, który ma dostęp do środowiska usługi Azure Stack.
2. Uruchom następujące polecenia w oknie programu PowerShell z podwyższonym poziomem uprawnień:

    ```powershell  
    $CloudAdminUser = "<cloud admin username>"
    $CloudAdminPassword = ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
    $stampInfoCreds = New-Object System.Management.Automation.PSCredential($CloudAdminUser, $CloudAdminPassword)
    $params = Invoke-RestMethod -Method Get -Uri 'https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation' -Credential $stampInfoCreds
    ConvertTo-Json $params > stampinfoproperties.json
    ```

### <a name="locate-values-in-the-ece-configuration-file"></a>Znajdź wartości w pliku konfiguracyjnym NZ

Wartości parametrów środowiska można również ręcznie się znajdować w **plik konfiguracyjny ONZ** znajdującym się w `C:\EceStore\403314e1-d945-9558-fad2-42ba21985248\80e0921f-56b5-17d3-29f5-cd41bf862787` na Menedżer DVM.

## <a name="test-parameters"></a>Parametry testu

Wspólne parametry testu zawierają poufne informacje, które nie mogą być przechowywane w plikach konfiguracji. Te muszą zostać dostarczone ręcznie.

Parametr    | Opis
-------------|-----------------
Użytkownik Administrator dzierżawy                            | Usługa Azure Active Directory administratora dzierżawy, który została aprowizowana przez administratora usługi w katalogu usługi AAD. Ten użytkownik wykonuje działania na poziomie dzierżawy takich jak wdrażanie szablonów, aby skonfigurować zasoby (maszyny wirtualne, konta magazynu, itp.) i wykonywanie obciążeń. Aby uzyskać szczegółowe informacje dotyczące inicjowania obsługi administracyjnej konta dzierżawy, zobacz [Dodaj nową dzierżawę usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-new-user-aad).
Użytkownik Administrator usługi             | Active Directory Administrator usługi Azure określony podczas wdrażania usługi Azure Stack dzierżawy katalogu usługi AAD. Wyszukaj `AADTenant` w konfiguracji ONZ plik i wybierz wartość `UniqueName` elementu.
Użytkownik Administrator chmury               | Konto administratora domeny w usłudze Azure Stack (na przykład `contoso\cloudadmin`). Wyszukaj `User Role="CloudAdmin"` w konfiguracji ONZ plik i wybierz wartość `UserName` elementu.
Parametry połączenia diagnostyki          | Adres URL sygnatury dostępu Współdzielonego do konta usługi Azure Storage do diagnostyki, które dzienniki będą kopiowane podczas wykonywania testów. Aby uzyskać instrukcje dotyczące generowania adresu URL sygnatury dostępu Współdzielonego, zobacz [wygenerować parametry połączenia diagnostyki](#generate-the-diagnostics-connection-string). |

> [!IMPORTANT]
> **Parametry połączenia diagnostyki** musi być prawidłowy przed kontynuowaniem.

### <a name="generate-the-diagnostics-connection-string"></a>Generowanie parametrów połączenia diagnostyki

Parametry połączenia diagnostyki są wymagane do przechowywania dzienników diagnostycznych podczas wykonywania testów. Użyj konta magazynu platformy Azure utworzone podczas konfiguracji (zobacz [Konfigurowanie walidacji jako zasoby usługi](azure-stack-vaas-set-up-resources.md)) do tworzenia podpisu (SAS) adresu URL dostępu współdzielonego do udostępnienia VaaS przekazywania dzienników do swojego konta magazynu.

1. [!INCLUDE [azure-stack-vaas-sas-step_navigate](includes/azure-stack-vaas-sas-step_navigate.md)]

1. Wybierz **Blob** z **opcji dozwolone usług**. Usuń zaznaczenie pozostałych opcji.

1. Wybierz **usługi**, **kontenera**, i **obiektu** z **dozwolone typy zasobów**.

1. Wybierz **odczytu**, **zapisu**, **listy**, **Dodaj**, **tworzenie** z **dozwolone uprawnienia**. Usuń zaznaczenie pozostałych opcji.

1. Ustaw **czas rozpoczęcia** do bieżącego czasu i **czas zakończenia** na trzy miesiące od bieżącego czasu.

1. [!INCLUDE [azure-stack-vaas-sas-step_generate](includes/azure-stack-vaas-sas-step_generate.md)]

> [!NOTE]  
> Adres URL sygnatury dostępu Współdzielonego wygasa w momencie zakończenia określony podczas generowania adresu URL.  
Podczas planowania testów, upewnij się, że adres URL jest prawidłowy dla co najmniej 30 dni oraz czas wymagany do wykonania testu (sugerowany trzy miesiące).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [weryfikacji jako kluczowe pojęcia dotyczące usługi](azure-stack-vaas-key-concepts.md)