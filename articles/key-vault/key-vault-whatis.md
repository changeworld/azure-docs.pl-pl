---
title: Co to jest usługa Azure Key Vault? | Microsoft Docs
description: Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą usługi Azure Key Vault klient może szyfrować klucze i klucze tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki PFX oraz hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM, hardware security module).
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/26/2017
ms.author: barclayn
ms.openlocfilehash: 1fd39cf6363cb028b2f933934c95ea2b635b754a
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089309"
---
# <a name="what-is-azure-key-vault"></a>Co to jest usługa Azure Key Vault?
Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą usługi Key Vault możesz szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki PFX oraz hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM, hardware security module). W celu zapewnienia dodatkowego bezpieczeństwa możesz zaimportować lub wygenerować klucze w modułach HSM. Jeśli się na to zdecydujesz, firma Microsoft będzie przetwarzać klucze w modułach HSM zweryfikowanych w trybie FIPS 140-2 poziom 2 (sprzęt i oprogramowanie układowe).  

Usługa Key Vault usprawnia proces zarządzania kluczami i pozwala zachować kontrolę nad kluczami, które mają dostęp do danych i szyfrują je. Deweloperzy mogą w klika minut utworzyć klucze do programowania i testowania, a następnie bezproblemowo przeprowadzić ich migrację do kluczy produkcji. W razie potrzeby administratorzy zabezpieczeń mogą przydzielić (i cofnąć) uprawnienia do używania kluczy.

Użyj poniższej tabeli, aby lepiej zrozumieć, w jaki sposób usługa Key Vault może pomóc deweloperom i administratorom zabezpieczeń w spełnianiu ich potrzeb.

| Rola | Opis problemu | Rozwiązanie usługi Azure Key Vault |
| --- | --- | --- |
| Deweloper aplikacji platformy Azure |„Chcę napisać aplikację dla platformy Azure, która używa kluczy do logowania i szyfrowania, ale chcę, żeby te klucze znajdowały się poza moją aplikacją tak, aby rozwiązanie było odpowiednie dla aplikacji rozproszonej geograficznie. <br/><br/>Chcę także, aby klucze i klucze tajne były chronione, ale bez konieczności samodzielnego pisania kodu. Powinny też być łatwe w użyciu w moich aplikacjach i mieć optymalną wydajność”. |√ Klucze są przechowywane w magazynie i w razie potrzeby wywoływane przez identyfikator URI.<br/><br/> √ Klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM).<br/><br/> √ Klucze są przetwarzane w modułach HSM, które znajdują się w tych samych centrach danych platformy Azure co aplikacje. Zapewnia to lepszą niezawodność i mniejsze opóźnienia, niż gdyby klucze były przechowywane w osobnej lokalizacji, na przykład lokalnie. |
| Deweloper oprogramowania jako usługi (SaaS) |„Nie chcę ponosić odpowiedzialności ani mieć potencjalnych problemów względem kluczy i kluczy tajnych dzierżawy moich klientów. <br/><br/>Chcę, aby klienci mogli niezależnie zarządzać swoimi kluczami. Dzięki temu będę w stanie się skupić na tym, co robię najlepiej – na tworzeniu podstawowych funkcji oprogramowania”. |√ Klienci mogą importować własne klucze do platformy Azure i zarządzać nimi. Gdy aplikacja SaaS musi wykonać operacje kryptograficzne przy użyciu kluczy swoich klientów, usługa Key Vault wykonuje te operacje w jej imieniu. Aplikacja nie ma wglądu w klucze klientów. |
| Chief Security Officer (CSO) |„Chcę wiedzieć, że nasze aplikacje są zgodne z modułami HSM w trybie FIPS 140-2 poziom 2 w celu bezpiecznego zarządzania kluczami. <br/><br/>Chcę się upewnić, że moja organizacja kontroluje cykl życia klucza i monitoruje jego użycie. <br/><br/>I chociaż korzystamy z wielu usług i zasobów platformy Azure, chcę mieć możliwość zarządzania kluczami z jednej lokalizacji na platformie Azure”. |√ Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2.<br/><br/>√ Usługa Key Vault jest zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje klucze ani nie mogła ich wyodrębnić.<br/><br/>√ Rejestrowanie użycia klucza niemal w czasie rzeczywistym.<br/><br/>√ Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz na platformie Azure, które regiony są przez nie obsługiwane oraz które aplikacje ich używają. |

Każdy posiadacz subskrypcji Azure może tworzyć magazyny kluczy i z nich korzystać. Mimo że usługa Key Vault przynosi korzyści głównie deweloperom i administratorom zabezpieczeń, może być wdrażana i zarządzana przez administratora organizacji, który zarządza innymi usługami platformy Azure dla organizacji. Administrator może na przykład zalogować się przy użyciu subskrypcji platformy Azure, utworzyć dla organizacji magazyn, w którym będą przechowywane klucze, a następnie odpowiadać za takie zadania operacyjne jak:

* Tworzenie lub importowanie klucza lub klucza tajnego
* Odwoływanie lub usuwanie klucza lub klucza tajnego
* Zezwalanie użytkownikom lub aplikacjom na dostęp do magazynu kluczy, aby mogli zarządzać kluczami i kluczami tajnymi lub używać ich
* Konfigurowanie użycia klucza (na przykład rejestrowanie lub szyfrowanie)
* Monitorowanie użycia klucza

Administrator może następnie dostarczyć deweloperom identyfikatory URI w celu wywoływania z aplikacji oraz dostarczyć administratorom zabezpieczeń informacje o rejestrowaniu użycia klucza. 

   ![Omówienie usługi Azure Key Vault][1]

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji, zobacz artykuł [Przewodnik dewelopera usługi Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Następne kroki
Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [Wprowadzenie do usługi Azure Key Vault](key-vault-get-started.md).

Aby uzyskać więcej informacji na temat rejestrowania użycia usługi Key Vault, zobacz [Rejestrowanie usługi Azure Key Vault](key-vault-logging.md).

Aby uzyskać więcej informacji na temat używania kluczy i kluczy tajnych w usłudze Azure Key Vault, zobacz [Informacje o kluczach, kluczach tajnych i certyfikatach](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
