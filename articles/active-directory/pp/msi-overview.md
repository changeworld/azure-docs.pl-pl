---
title: Zarządzane tożsamości usługi (MSI) dla usługi Azure Active Directory
description: Przegląd zarządzane tożsamości usługi dla zasobów platformy Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 95980c082b09ad959ab8bbaae0250b40ac08d2c8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Zarządzane tożsamości usługi (MSI) dla zasobów platformy Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Typowe wyzwanie podczas tworzenia aplikacji w chmurze jest sposób zarządzania poświadczeniami, które powinny znajdować się w kodzie do uwierzytelniania usługi w chmurze. Dzięki te poświadczenia bezpieczna jest ważnym zadaniem. W idealnym przypadku one nigdy nie są wyświetlane na stacjach roboczych deweloperów lub pobrać wyewidencjonowany do kontroli źródła. Usługa Azure Key Vault zapewnia bezpieczne przechowywanie poświadczeń i innych kluczy i kluczy tajnych, ale kodu musi zostać uwierzytelniona Key Vault w celu ich pobrania. Tożsamość usługi zarządzanej (MSI) powoduje, że rozwiązania tego problemu prostszy, zapewniając usług Azure automatycznie zarządzane tożsamości w usłudze Azure Active Directory (Azure AD). Ta tożsamość służy do uwierzytelniania do dowolnej usługi obsługującej uwierzytelniania usługi Azure AD, w tym usługi Key Vault bez żadnych poświadczeń w kodzie.

## <a name="how-does-it-work"></a>Jak to działa?

Korzystając z tożsamością usługi zarządzane w wystąpieniu usługi Azure, Azure tworzy tożsamość w dzierżawie usługi Azure AD używane przez subskrypcję platformy Azure. Ponadto Azure udostępnia poświadczenia tożsamości, na wystąpienie usługi. W związku z tym kodzie można następnie wysłać żądanie lokalne uzyskanie tokenów dostępu do usług, które obsługują usługi Azure AD authentication. Wszystkie, podczas gdy platforma Azure zapewnia obsługę stopniowych poświadczenia używane przez wystąpienie usługi.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>Jak włączyć zasoby do korzystania z tożsamości zarządzanego usługi?

Istnieją dwa typy zarządzane tożsamości usługi: *przypisane systemu* i *przypisany użytkownik*.

- A **przypisane systemu** MSI jest włączona bezpośrednio w wystąpieniu usługi Azure. Za pośrednictwem procesu włączania Azure tworzy tożsamość wystąpienia usługi w dzierżawie usługi Azure AD i przepisy poświadczenia tożsamości na wystąpienie usługi. Cykl życia systemu przypisanej MSI jest bezpośrednio związany z platformy Azure wystąpienie usługi jest on włączony na. Jeśli wystąpienie usługi zostanie usunięty, Azure automatycznie oczyszcza poświadczenia i tożsamość w usłudze Azure AD.

- A **przypisany użytkownik** MSI *(podglądzie prywatnym)* zostanie utworzona jako autonomiczny zasobów platformy Azure. Proces tworzenia Azure tworzy tożsamość w dzierżawie usługi Azure AD. Po utworzeniu tożsamości mogą być przypisane do co najmniej jedno wystąpienie usługi Azure. Ponieważ pliku MSI przypisane przez użytkownika mogą być używane przez wiele wystąpień usługi Azure, jego cyklu życia zarządzanym oddzielnie.

Oto przykład działania Instalatora MSI przypisane system z maszyn wirtualnych platformy Azure.

![Przykład MSI maszyny wirtualnej](~/articles/active-directory/media/msi-vm-example.png)

1. Usługa Azure Resource Manager odbiera wiadomość, aby włączyć MSI przypisane systemu na maszynie Wirtualnej.
2. Usługa Azure Resource Manager tworzy nazwy głównej usługi w usłudze Azure AD do reprezentowania tożsamości maszyny wirtualnej. Nazwy głównej usługi jest tworzony w dzierżawie usługi Azure AD, który jest zaufany dla tej subskrypcji.
3. Usługa Azure Resource Manager konfiguruje szczegóły nazwy głównej usługi w pliku MSI rozszerzenia maszyny Wirtualnej maszyny wirtualnej. Ten krok obejmuje Konfigurowanie identyfikator klienta i certyfikat używany przez rozszerzenie do uzyskania dostępu do tokenów z usługi Azure AD.
4. Teraz, nosi nazwę główną usługi tożsamość maszyny Wirtualnej, może zostać przydzielony dostęp do zasobów platformy Azure. Na przykład jeśli kod wymaga wywołań usługi Azure Resource Manager, następnie należy przypisywanej nazwy głównej usługi maszyny Wirtualnej odpowiednią rolę przy użyciu kontroli dostępu opartej na rolach (RBAC) w usłudze Azure AD.  Kod musi wywołać Key Vault, czy udzielić kodu dostępu do określonego hasła lub klucza w magazynie kluczy.
5. Kod uruchomiony na maszynie Wirtualnej żądania tokenu z lokalnego punktu końcowego, który jest obsługiwany przez rozszerzenie maszyny Wirtualnej MSI: http://localhost:50342/oauth2/token. Parametr zasobu określa usługi, do którego wysyłane jest token. Na przykład, jeśli chcesz, aby kod do uwierzytelniania w usłudze Azure Resource Manager, można skorzystać zasobu =https://management.azure.com/.
6. MSI rozszerzenia maszyny Wirtualnej używa Identyfikatora klienta skonfigurowanego i certyfikatu poproś token dostępu z usługi Azure AD.  Usługi Azure AD zwraca token dostępu tokenu Web JSON (JWT).
7. Kod wysyła ten token dostępu na wywołanie do usługi, która obsługuje uwierzytelnianie w usłudze Azure AD.

Przy użyciu tego samego diagramu, tutaj przykład działania Instalatora MSI przypisane przez użytkownika z maszyn wirtualnych platformy Azure.

![Przykład MSI maszyny wirtualnej](~/articles/active-directory/media/msi-vm-example.png)

1. Usługa Azure Resource Manager odbiera wiadomość w celu utworzenia pliku MSI przypisane przez użytkownika.
2. Usługa Azure Resource Manager tworzy nazwy głównej usługi w usłudze Azure AD do reprezentowania tożsamości tego MSI. Nazwy głównej usługi jest tworzony w dzierżawie usługi Azure AD, który jest zaufany dla tej subskrypcji.
3. Usługa Azure Resource Manager odbierze komunikat, aby skonfigurować szczegóły nazwy głównej usługi w pliku MSI rozszerzenia maszyny Wirtualnej maszyny wirtualnej. Ten krok obejmuje Konfigurowanie identyfikator klienta i certyfikat używany przez rozszerzenie do uzyskania dostępu do tokenów z usługi Azure AD.
4. Teraz, gdy tożsamość nazwy głównej usługi MSI jest znana, będzie można mu przyznać dostęp do zasobów platformy Azure. Na przykład jeśli kod wymaga wywołań usługi Azure Resource Manager, następnie należy przypisywanej nazwy głównej usługi MSI odpowiednią rolę przy użyciu kontroli dostępu opartej na rolach (RBAC) w usłudze Azure AD. Kod musi wywołać Key Vault, czy udzielić kodu dostępu do określonego hasła lub klucza w magazynie kluczy. Uwaga: Nie jest wymagane do wykonania kroku 4 krok 3. Gdy istnieje MSI, będzie można mu przyznać dostęp do zasobów, niezależnie od tego, są skonfigurowane na maszynie Wirtualnej, lub nie.
5. Kod uruchomiony na maszynie Wirtualnej żądania tokenu z lokalnego punktu końcowego, który jest obsługiwany przez rozszerzenie maszyny Wirtualnej MSI: http://localhost:50342/oauth2/token. Z parametru Identyfikatora klienta Określa nazwę pliku MSI tożsamości do użycia. Ponadto parametr zasobu określa usługi, do którego wysyłane jest token. Na przykład, jeśli chcesz, aby kod do uwierzytelniania w usłudze Azure Resource Manager, można skorzystać zasobu =https://management.azure.com/.
6. Rozszerzenia maszyny Wirtualnej MSI sprawdza, jeśli certyfikat dla tego Identyfikatora klienta żądanego jest skonfigurowany i żądania tokenu dostępu z usługi Azure AD. Usługi Azure AD zwraca token dostępu tokenu Web JSON (JWT).
7. Kod wysyła ten token dostępu na wywołanie do usługi, która obsługuje uwierzytelnianie w usłudze Azure AD.

Każdy usługa Azure, która obsługuje zarządzane tożsamości usługi ma własną metodę dla kodu można uzyskać tokenu dostępu. Zapoznaj się z samouczkami dla każdej usługi dowiedzieć się, określonej metody w celu pobrania tokenu.

## <a name="try-managed-service-identity"></a>Spróbuj tożsamość usługi zarządzanej

Spróbuj samouczek zarządzane tożsamość usługi, aby dowiedzieć się więcej na trasie scenariusze dostępu do różnych zasobów platformy Azure:
<br><br>
| Z zasobów włączone MSI | Dowiedz się, jak |
| ------- | -------- |
| Maszyna wirtualna platformy Azure (Linux)   | [Tożsamość usługi zarządzanej dostępu usługi Azure Resource Manager z maszyny Wirtualnej systemu Linux](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Dostęp do magazynu Azure za pomocą klucza dostępu z tożsamością usługi zarządzania maszyny Wirtualnej systemu Linux](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Usługi platformy Azure obsługuje zarządzane tożsamość usługi?

Usług Azure, które obsługują zarządzane tożsamość usługi umożliwia uwierzytelniania w usłudze, które obsługują usługi Azure AD authentication MSI.  Trwa integrowanie uwierzytelniania MSI i Azure AD na platformie Azure.  Sprawdź wstecz często dla aktualizacji.

### <a name="azure-services-that-support-managed-service-identity"></a>Usług Azure, które obsługują zarządzane tożsamości usługi

Następujących usług platformy Azure obsługuje zarządzane tożsamości usługi.

| Usługa | Stan | Date | Konfigurowanie | Uzyskaj token |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Wersja zapoznawcza | 2017 września | [Interfejs wiersza polecenia platformy Azure](msi-qs-configure-cli-windows-vm.md)<br>[Szablony usługi Azure Resource Manager](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure usługi uwierzytelniania pomocy technicznej usługi Azure AD

Następujące usługi obsługi uwierzytelniania usługi Azure AD i zostały przetestowane z klienta usług, które korzystają zarządzane tożsamości usługi.

| Usługa | Identyfikator zasobu | Stan | Date | Przypisywanie dostępu |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Dostępna | 2017 września | [Interfejs wiersza polecenia platformy Azure](msi-howto-assign-access-CLI.md) |
| W usłudze Azure Key Vault | https://vault.azure.net/ | Dostępna | 2017 września | |
| Azure Data Lake | https://datalake.azure.net/ | Dostępna | 2017 września | |
| Azure SQL | https://database.windows.net/ | Dostępna | 2017 października | |

## <a name="how-much-does-managed-service-identity-cost"></a>Ile kosztuje zarządzane tożsamości usługi

Tożsamość usługi zarządzanej pochodzi z usługi Azure Active Directory bez, co jest ustawieniem domyślnym dla subskrypcji platformy Azure.  Nie ma żadnych dodatkowych kosztów dla zarządzanych tożsamości usługi.

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Chcielibyśmy usłyszeć Twoja!

* Zadaj pytania instrukcje na stronie Stack Overflow znacznikiem [azure msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Funkcja żądania lub Prześlij opinię na temat [forum opinii usługi Azure AD dla deweloperów](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






