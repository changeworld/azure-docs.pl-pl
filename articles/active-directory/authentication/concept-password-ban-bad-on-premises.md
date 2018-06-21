---
title: Podgląd ochrony hasła w usłudze Azure AD
description: Zakaz słabe hasła w lokalnej Active Directory przy użyciu podglądu ochrony hasła usługi Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 0819a947229e633331253923654de56638a6c76a
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296113"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Podgląd: Wymuszanie ochrony hasłem usługi Azure AD dla usługi Active Directory systemu Windows Server

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy zabronionych hasła niestandardowego są funkcje publicznej wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [uzupełniające warunki użytkowania dotyczącym wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Ochrony hasłem w usłudze Azure AD jest nową funkcją w publicznej wersji zapoznawczej obsługiwane przez usługę Azure Active Directory (Azure AD) w celu zwiększenia zasad haseł w organizacji. Lokalnego wdrożenia usługi Azure AD hasło ochrony używa zarówno globalnych i niestandardowe zakazane listy haseł przechowywanych w usłudze Azure AD i wykonuje samej kontroli lokalną jako zmiany oparte na chmurze usługi Azure AD.

Istnieją trzy składniki oprogramowania, które tworzą ochrony hasłem usługi Azure AD:

* Uruchamia usługę serwera proxy usługi Azure AD hasło ochrony na dowolnym komputerze przyłączonym do domeny w bieżącym lesie usługi Active Directory. Przekazuje żądania z kontrolerów domeny do usługi Azure AD, a zwraca odpowiedź z usługi Azure AD do kontrolera domeny.
* Usługa agenta ochrony DC hasła usługi Azure AD odbiera żądania weryfikacji haseł z biblioteki dll filtru haseł agenta z kontrolera domeny, przetwarza je za pomocą bieżące zasady haseł dostępne lokalnie i zwraca wynik (pass\fail). Ta usługa jest odpowiedzialna za okresowo (raz na godzinę) wywołanie usługi Azure AD hasło ochrony serwera proxy do pobierania nowych wersji zasad haseł. Komunikacji dla wywołań do i z usługi Azure AD hasło ochrony serwera proxy jest obsługiwany za pośrednictwem wywołania RPC (zdalne wywoływanie procedury) za pośrednictwem protokołu TCP. Podczas pobierania nowe zasady są przechowywane w folderze sysvol, gdzie można replikować do innych kontrolerów domeny. Usługa agenta DC monitoruje również folder sysvol dla zmian w przypadku innych kontrolerów domeny, musieli napisać nowe zasady haseł, jeśli odpowiednio ostatnie zasad jest już dostępne wyboru usługi serwera proxy ochrony hasła usługi Azure AD zostaną pominięte.
* Biblioteki dll filtru haseł agenta DC odbiera żądania weryfikacji haseł z systemu operacyjnego i przekazuje je do usługi agenta ochrony DC hasła usługi Azure AD uruchomiony lokalnie na kontrolerze domeny.

![W jaki sposób składniki ochrony hasła usługi Azure AD współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

## <a name="requirements"></a>Wymagania

* Wszystkie komputery, na którym są zainstalowane składniki ochrony hasła usługi Azure AD w tym kontrolerów domeny musi działać system Windows Server 2012 lub nowszym.
* Między co najmniej jeden kontroler domeny w każdej domenie i co najmniej jeden serwer obsługujący usługę Azure AD hasło ochrony serwera proxy musi istnieć łączność sieciową.
* Dowolnej domeny usługi Active Directory z kontrolera domeny oprogramowanie usługi agenta należy użyć DFSR do replikacji folderu sysvol.
* Konta administratora globalnego, aby zarejestrować usługę serwera proxy ochrony hasła usługi Azure AD z usługą Azure AD.
* Konta z uprawnieniami administratora domeny usługi Active Directory w domenie głównej lasu.

### <a name="license-requirements"></a>Wymagania licencyjne

Korzyści wynikające z listy globalnego hasła zabronione mają zastosowanie do wszystkich użytkowników usługi Azure Active Directory (Azure AD).

Na liście hasła niestandardowego zabronione wymaga licencji Azure AD podstawowa.

Azure AD ochrony hasłem dla systemu Windows Server Active Directory wymaga licencji usługi Azure AD Premium. 

Dodatkowe informacje licencyjne, wraz z kosztami, można znaleźć w [usługi Azure Active Directory cennik lokacji](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Do pobrania

Istnieją dwa wymagane pliki instalacyjne ochrony hasłem usługi Azure AD, który można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Odpowiedzi na często zadawane pytania

* Nie wymaganego z kontrolerów domeny łączności z Internetem. Uruchomiona usługa serwera proxy usługi Azure AD hasło ochrony maszyn są tylko maszyny, które wymagają łączności z Internetem.
* Nie porty są otwarte na kontrolerach domeny.
* Zmiany schematu usługi Active Directory, nie są wymagane.
   * Oprogramowanie korzysta z istniejącego kontenera usługi Active Directory i obiektów schematu serviceConnectionPoint.
* Nie jest minimalna domeny usługi Active Directory lub funkcjonalności lasu poziomu (DFL\FFL) wymagany.
* Oprogramowanie nie tworzy ani nie wymagają żadnych kont w domenach usługi Active Directory, które chroni.
* Wdrożenie przyrostowe jest obsługiwane z zależnościami, że hasła są wymuszane tylko z zainstalowanym agentem kontrolera domeny.
* Ochrony hasłem w usłudze Azure AD nie jest aparat aplikacji w czasie rzeczywistym zasad. Mogą występować opóźnienia w czas od zmiany konfiguracji zasad haseł podczas osiągnie i są wymuszane na wszystkich kontrolerach domeny.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises.md)