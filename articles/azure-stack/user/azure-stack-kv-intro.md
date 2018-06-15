---
title: Wprowadzenie stosu magazynu kluczy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak magazyn kluczy Azure stosu zarządza kluczy i kluczy tajnych
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: mabrigg
ms.openlocfilehash: a50a03e70ccf014a8a9d33e0f177febed560853f
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
ms.locfileid: "26636730"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Wprowadzenie do magazynu kluczy Azure stosu

## <a name="prerequisites"></a>Wymagania wstępne 

* Należy zasubskrybować ofertę, która obejmuje usługę Azure Key Vault.  
* [Środowisko PowerShell jest skonfigurowany do użycia z stosu Azure](azure-stack-powershell-configure-user.md).
 
## <a name="key-vault-basics"></a>Podstawy usługi Key Vault
Magazyn kluczy Azure stosu pomaga w zabezpieczaniu kluczy kryptograficznych i użyj klucze tajne, które aplikacje i usługi w chmurze. Za pomocą usługi Key Vault, można szyfrować klucze i klucze tajne, takich jak:
   * Klucze uwierzytelniania 
   * Klucze konta magazynu
   * Klucze szyfrowania danych
   * pliki PFX
   * Hasła

Usługa Key Vault usprawnia proces zarządzania kluczami i pozwala zachować kontrolę nad kluczami, które mają dostęp do danych i szyfrują je. Deweloperzy mogą w klika minut utworzyć klucze do programowania i testowania, a następnie bezproblemowo przeprowadzić ich migrację do kluczy produkcji. W razie potrzeby administratorzy zabezpieczeń mogą przydzielić (i cofnąć) uprawnienia do używania kluczy.

Każdy z subskrypcją platformy Azure stosu można tworzyć i używać magazynów kluczy. Mimo że Key Vault przynosi korzyści deweloperom i administratorom zabezpieczeń, operator, który zarządza innymi usługami Azure stosu dla organizacji, można zaimplementować i zarządzać nią. Na przykład operator może zalogować się przy użyciu subskrypcji platformy Azure stosu stosu Azure Tworzenie magazynu dla organizacji, w którym do przechowywania kluczy, a następnie odpowiadać za takie zadania operacyjne:

* Tworzenie lub Importowanie klucza lub klucza tajnego.
* Odwoływanie lub usuwanie klucza lub klucza tajnego.
* Zezwalanie użytkownikom lub aplikacjom dostęp do magazynu kluczy, więc można następnie zarządzać lub używać jej kluczy i kluczy tajnych.
* Konfigurowanie użycia klucza (na przykład, podpisywania lub szyfrowania).

Operator może następnie dostarczyć deweloperom Uniform Resource Identifier (URI), do wywoływania z aplikacji. Operatory może także udostępnić administratorom zabezpieczeń informacje o rejestrowaniu użycia klucza.

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji zobacz przewodnik dewelopera usługi Key Vault.

## <a name="scenarios"></a>Scenariusze
W poniższych scenariuszach opisano, jak usługi Key Vault może pomóc potrzeb deweloperom i administratorom zabezpieczeń.

### <a name="developer-for-an-azure-stack-application"></a>Deweloper aplikacji Azure stosu
**Problem:** chcę napisać aplikację dla stosu Azure, która używa kluczy do podpisywania i szyfrowania. Chcę się poza moją aplikacją tak, że rozwiązanie jest odpowiednie dla aplikacji rozproszonej geograficznie tych kluczy.

**Instrukcja:** klucze są przechowywane w magazynie i wywoływane przez identyfikator URI, w razie potrzeby.

### <a name="developer-for-software-as-a-service-saas"></a>Deweloper oprogramowania jako usługa (SaaS)
**Problem:** nie chcę odpowiedzialności odpowiedzialności lub potencjalne kluczy i kluczy tajnych przez klienta. Chcę, aby klienci mogli niezależnie zarządzać swoimi kluczami, dzięki czemu można I skoncentrować się na ten co robię najlepiej, który udostępnia podstawowe funkcje oprogramowania.

**Instrukcja:** klientów można importować własne klucze do stosu usługi Azure, a następnie zarządzanie nimi. 

### <a name="chief-security-officer-cso"></a>Chief Security Officer (CSO)
**Problem:** chcę upewnić się, że Moja organizacja kontroluje cykl życia klucza i monitorować użycie klucza.

**Instrukcja:** Key Vault jest zaprojektowana tak, aby Microsoft Zobacz lub nie wyodrębnić klucze. W przypadku aplikacji musi wykonują operacje kryptograficzne przy użyciu kluczy klientów, Key Vault używa kluczy w imieniu aplikacji. Aplikacja nie ma wglądu w klucze klientów. Chociaż korzystamy z wielu usług Azure stosu i zasobów, można zarządzać kluczami z jednej lokalizacji w stosie Azure. Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz w stosie Azure, które regiony one pomocy technicznej i które aplikacje ich używają.

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie Key Vault w stosie Azure przy użyciu portalu](azure-stack-kv-manage-portal.md)  
* [Zarządzanie Key Vault w stosie Azure przy użyciu programu PowerShell](azure-stack-kv-manage-powershell.md)

