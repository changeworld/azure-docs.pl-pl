---
title: Wspólne parametry przepływów pracy w usłudze Azure Stack weryfikacji jako usługa | Dokumentacja firmy Microsoft
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
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 81a7be973739cfd6eb3f8fb8dc7a0723623c2b8e
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235051"
---
# <a name="workflow-common-parameters-for-azure-stack-validation-as-a-service"></a>Wspólne parametry przepływów pracy dla usługi Azure Stack weryfikacji jako usługa

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Wspólne parametry zawierają wartości, takie jak zmienne środowiskowe i użytkownika poświadczeń wymaganych przez wszystkie testy sprawdzania poprawności jako usługa (VaaS). Należy zdefiniować te wartości na poziomie przepływu pracy. Zapisz te wartości, podczas tworzenia lub modyfikowania przepływu pracy. W czasie harmonogram przepływ pracy ładuje wartości do testu. 

## <a name="environment-parameters"></a>Parametry środowiska

Parametry środowiska opisują środowiska usługi Azure Stack w ramach testu. Te wartości muszą być dostarczone przez generowanie i przekazywanie pliku konfiguracji sygnatury `&lt;link&gt;. [How to get the stamp info link].`

| Nazwa parametru | Wymagane | Typ | Opis |
|----------------------------------|----------|------|---------------------------------------------------------------------------------------------------------------------------------|
| Usługa Azure Stack kompilacji | Wymagane |  | Wdrożenie usługi Azure Stack (na przykład 1.0.170330.9) numer kompilacji |
| Wersja producenta OEM | Yes |  | Numer wersji pakietu OEM, używane podczas wdrażania usługi Azure Stack. |
| Podpis producenta OEM | Yes |  | Podpis pakietu OEM, używane podczas wdrażania usługi Azure Stack. |
| Identyfikator dzierżawy usługi AAD | Wymagane |  | Dzierżawa usługi Azure Active Directory GUID określony podczas wdrażania usługi Azure Stack.|
| Region | Wymagane |  | Region wdrożenia platformy Azure Stack. |
| Punkt końcowy dzierżawy Resource Manager | Wymagane |  | Punkt końcowy dla dzierżawy usługi Azure Resource Manager operacje (na przykład https://management.<ExternalFqdn>) |
| Punkt końcowy administratora Resource Manager | Yes |  | Punkt końcowy dla operacji dzierżawy usługi Azure Resource Manager (na przykład https://adminmanagement.<ExternalFqdn>) |
| Nazwa FQDN zewnętrznej | Yes |  | Zewnętrzne w pełni kwalifikowana nazwa domeny używana jako sufiks dla punktów końcowych. (na przykład local.azurestack.external lub redmond.contoso.com). |
| Liczba węzłów | Yes |  | Liczba węzłów we wdrożeniu. |

## <a name="test-parameters"></a>Parametry testu

Wspólne parametry testu zawierają poufne informacje, które nie przechowywane w plikach konfiguracji i musi zostać dostarczone ręcznie.

| Nazwa parametru | Wymagane | Typ | Opis |
|--------------------------------|------------------------------------------------------------------------------|------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nazwa użytkownika dzierżawy | Wymagane |  | Usługa Azure dzierżawy administrator usługi Active Directory, albo została już przygotowana, lub musi być obsługiwana przez administratora usługi w katalogu usługi AAD. Aby uzyskać szczegółowe informacje dotyczące inicjowania obsługi administracyjnej konta dzierżawy, zobacz [Rozpoczynanie pracy z usługą Azure AD](https://docs.microsoft.com/azure/active-directory/get-started-azure-ad). Ta wartość jest używana przez test do wykonywania operacji na poziomie dzierżawy takich jak wdrażanie szablonów do aprowizacji zasobów (maszyn wirtualnych, kont magazynu itp.), a następnie wykonaj obciążeń. Ta wartość jest używana przez test do wykonywania operacji na poziomie dzierżawy takich jak wdrażanie szablonów do aprowizacji zasobów (maszyn wirtualnych, kont magazynu itp.), a następnie wykonaj obciążeń. |
| Hasło dzierżawy | Wymagane |  | Hasło dla użytkownika dzierżawy. |
| Nazwa użytkownika administratora usługi | Wymagane: Rozwiązanie sprawdzania poprawności, sprawdzanie poprawności pakietu<br>Niewymagane: przebieg testu |  | Usługa Azure administrator usługi Active Directory dzierżawy katalogu usługi AAD, określony podczas wdrażania usługi Azure Stack. |
| Hasło administratora usługi | Wymagane: Rozwiązanie sprawdzania poprawności, sprawdzanie poprawności pakietu<br>Niewymagane: przebieg testu |  | Hasło dla użytkownika administratora usługi. |
| Nazwa użytkownika administratora chmury | Wymagane |  | Usługa Azure Stack konta administratora domeny (na przykład contoso\cloudadmin). Wyszukaj rolę użytkownika = "CloudAdmin" w pliku konfiguracji i wybierz wartość w tag nazwy użytkownika w pliku konfiguracji. |
| Hasło administratora chmury | Wymagane |  | Hasło dla użytkownika Administrator w chmurze. |
| Parametry połączenia diagnostyki | Wymagane |  | Identyfikator URI sygnatury dostępu Współdzielonego do konta usługi Azure Storage do diagnostyki, które dzienniki będą kopiowane podczas wykonywania testów. Znajdują się instrukcje dotyczące generowania identyfikatora URI połączenia SAS [Konfigurowanie konta usługi blob storage](azure-stack-vaas-set-up-account.md). |


## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się więcej na temat [walidacji usługi Azure Stack jako usługa](https://docs.microsoft.com/azure/azure-stack/partner).
