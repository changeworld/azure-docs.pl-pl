---
title: Wprowadzenie do usługi Azure Stack Key Vault | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługa Azure Stack Key Vault zarządza kluczy i wpisów tajnych
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/05/2019
ms.author: sethm
ms.openlocfilehash: 40a748895038dbadea7d12d79268f89796a4b428
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2019
ms.locfileid: "54061505"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Wprowadzenie do usługi Key Vault w usłudze Azure Stack

## <a name="prerequisites"></a>Wymagania wstępne

* Należy subskrybować ofertę, która obejmuje usługę Azure Key Vault.  
* [Program PowerShell jest skonfigurowana do użycia z usługą Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="key-vault-basics"></a>Podstawy usługi Key Vault

Key Vault w usłudze Azure Stack pomaga chronić klucze kryptograficzne i wpisy tajne, które aplikacje i usługi w chmurze Użyj. Za pomocą usługi Key Vault, umożliwia ona szyfrowanie kluczy i wpisów tajnych, takich jak:

* Klucze uwierzytelniania
* Klucze kont magazynu
* Klucze szyfrowania danych
* pliki PFX
* Hasła

Usługa Key Vault usprawnia proces zarządzania kluczami i pozwala zachować kontrolę nad kluczami, które mają dostęp do danych i szyfrują je. Deweloperzy mogą w klika minut utworzyć klucze do programowania i testowania, a następnie bezproblemowo przeprowadzić ich migrację do kluczy produkcji. Administratorzy zabezpieczeń można przydzielić (lub cofnąć) uprawnienia do kluczy, zgodnie z potrzebami.

Każda osoba z subskrypcją usługi Azure Stack można tworzyć i używać magazynów kluczy. Mimo że usługa Key Vault przynosi korzyści deweloperom i administratorom zabezpieczeń, operatora, który zarządza innymi usługami Azure Stack w organizacji można zaimplementować i zarządzać nią. Na przykład usługi Azure Stack, operator może zalogować się przy użyciu subskrypcji usługi Azure Stack, utworzyć magazyn dla organizacji, w którym do przechowywania kluczy, a następnie odpowiadać za takie zadania operacyjne:

* Tworzenie lub Importowanie klucza lub klucza tajnego.
* Odwoływanie lub usuwanie klucza lub klucza tajnego.
* Autoryzowanie użytkowników lub aplikacji na dostęp do magazynu kluczy, więc można następnie zarządzać lub użyj jej kluczy i wpisów tajnych.
* Konfigurowanie użycia klucza (na przykład podpisywanie lub szyfrowanie).

Operator może następnie udostępniać deweloperom za pomocą jednolitych identyfikatorów zasobów (URI), aby wywołać z poziomu ich aplikacji. Operatory można też podać administratorom zabezpieczeń informacje rejestrowania użycia klucza.

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji zobacz przewodnik dewelopera usługi Key Vault.

## <a name="scenarios"></a>Scenariusze

W poniższych scenariuszach opisano, jak usługa Key Vault może pomóc zaspokoić potrzeby deweloperom i administratorom zabezpieczeń.

### <a name="developer-for-an-azure-stack-application"></a>Deweloper aplikacji usługi Azure Stack

**Problem:** Chcę napisać aplikację dla usługi Azure Stack, która używa kluczy do podpisywania i szyfrowania. Chcę, żeby te klucze znajdowały się poza moją aplikacją tak, aby rozwiązanie było odpowiednie dla aplikacji rozproszonej geograficznie.

**Instrukcja:** Klucze są przechowywane w magazynie i wywoływane przez identyfikator URI, gdy potrzebne.

### <a name="developer-for-software-as-a-service-saas"></a>Deweloper oprogramowania jako usługi (SaaS)

**Problem:** Nie chcę ponosić odpowiedzialności ani mieć potencjalnych problemów względem kluczy i wpisów tajnych przez klienta. Chcę, aby klienci mogli niezależnie zarządzać swoimi kluczami tak, aby I może skupić się na tym najlepiej, co robię podstawowych funkcji oprogramowania.

**Instrukcja:** Klienci mogą importować własne klucze do usługi Azure Stack, a następnie zarządzać nimi.

### <a name="chief-security-officer-cso"></a>Chief Security Officer (CSO)

**Problem:** Chcę się upewnić, że moja organizacja kontroluje cykl życia klucza i monitoruje jego użycie.

**Instrukcja:** Usługa Magazyn kluczy jest zaprojektowana tak, że firma Microsoft nie zna Twoich kluczy ani nie może ich wyodrębnić. Gdy aplikacja musi wykonać operacje kryptograficzne przy użyciu kluczy klienta, usługa Key Vault używa kluczy w imieniu aplikacji. Aplikacja nie ma wglądu w klucze klientów. Chociaż korzystamy z wielu zasobów i usługi Azure Stack, mogą zarządzać kluczami z jednej lokalizacji w usłudze Azure Stack. Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz w usłudze Azure Stack, które regiony są pomocy technicznej i które aplikacje ich używają.

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie usługi Key Vault w usłudze Azure Stack w portalu](azure-stack-kv-manage-portal.md)  
* [Zarządzanie usługi Key Vault w usłudze Azure Stack przy użyciu programu PowerShell](azure-stack-kv-manage-powershell.md)

