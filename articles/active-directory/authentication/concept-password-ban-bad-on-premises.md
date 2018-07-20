---
title: Usługa Azure AD hasło protection w wersji zapoznawczej
description: Zablokuj słabe hasła w Active Directory w środowisku lokalnym za pomocą hasła usługi Azure AD protection (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 5d4062ff2c21deeb56eb4ae6e8386452608f85fa
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158808"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Wersja zapoznawcza: Wymuszanie ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy niestandardowej zakazanych haseł są publicznej wersji zapoznawczej funkcji usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Ochrony hasłem w usłudze Azure AD to nowa funkcja w wersji zapoznawczej obsługiwane przez usługi Azure Active Directory (Azure AD), aby zwiększyć zasady haseł w organizacji. Wdrażanie w środowisku lokalnym ochrona za pomocą hasła usługi Azure AD używa zarówno globalnych i niestandardowe zakazane listy haseł przechowywanych w usłudze Azure AD i wykonuje ten sam kontroli lokalnej zgodnie ze zmianami oparte na chmurze usługi Azure AD.

Istnieją trzy składniki oprogramowania, które tworzą ochrona za pomocą hasła usługi Azure AD:

* Usługa serwera proxy ochrony haseł usługi Azure AD działa na dowolnym komputerze przyłączonym do domeny w bieżącym lesie usługi Active Directory. Jego przekazuje żądania z kontrolerów domeny do usługi Azure AD i zwraca odpowiedź z usługi Azure AD do kontrolera domeny.
* Usługa agenta ochrony kontrolera domeny hasła usługi Azure AD odbiera żądania weryfikacji hasła z dll filtru haseł agenta kontrolera domeny, przetwarza je za pomocą bieżące zasady haseł dostępne lokalnie i zwraca wynik (pass\fail). Ta usługa jest odpowiedzialna za okresowo (raz na godzinę) podczas wywoływania usługi Azure AD hasło ochrony serwera proxy do pobierania nowych wersji zasad haseł. Komunikacji w przypadku wywołania do i z usługi Azure AD hasło ochrony serwera proxy odbywa się za pośrednictwem wywołania RPC (zdalne wywoływanie procedury) za pośrednictwem protokołu TCP. Podczas pobierania nowe zasady są przechowywane w folderze sysvol, gdzie można replikować na innych kontrolerach domeny. Usługa agenta DC monitoruje również folder sysvol dla zmian w przypadku, gdy inne kontrolery domeny zostały napisane ma nowe zasady haseł, odpowiednio ostatnich zasad jest już dostępna wyboru usługi Serwer proxy usługi Azure AD hasło ochrony spowoduje pominięcie.
* Dll filtru haseł agenta DC odbiera żądania weryfikacji hasła od systemu operacyjnego i przekazuje je do usługi agenta ochrony kontrolera domeny hasła usługi Azure AD działa lokalnie na kontrolerze domeny.

![W jaki sposób składniki ochrony hasła usługi Azure AD współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

## <a name="requirements"></a>Wymagania

* Wszystkie komputery z zainstalowanym składniki ochrony hasła usługi Azure AD w tym kontrolery domeny musi działać system Windows Server 2012 lub nowszy.
* Łączność sieciowa musi istnieć między co najmniej jeden kontroler domeny w każdej domenie i co najmniej jeden serwer obsługujący usługę serwera proxy ochrony haseł usługi Azure AD.
* Dowolnej domeny usługi Active Directory, uruchamianie kontrolera domeny oprogramowanie usługi agenta należy użyć DFSR do replikacji folderu sysvol.
* Konta administratora globalnego, aby zarejestrować usługę serwera proxy ochrony haseł usługi Azure AD z usługą Azure AD.
* Konta z uprawnieniami administratora domeny usługi Active Directory w domenie głównej lasu.

### <a name="license-requirements"></a>Wymagania licencyjne

Korzyści wynikające z listy globalne zakazanych haseł mają zastosowanie do wszystkich użytkowników usługi Azure Active Directory (Azure AD).

Listy niestandardowe zakazanych haseł wymaga licencji usługi Azure AD podstawowa.

Usługa Azure AD ochrona za pomocą hasła usługi Active Directory systemu Windows Server wymaga licencji usługi Azure AD Premium. 

Dodatkowe informacje o licencjonowaniu, wraz z kosztami, można znaleźć na [usługi Azure Active Directory ceny witryny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="download"></a>Do pobrania

Istnieją dwa wymagane pliki instalacyjne ochrony hasłem usługi Azure AD, który można pobrać z [Centrum pobierania firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="answers-to-common-questions"></a>Odpowiedzi na często zadawane pytania

* Nie wymaganych kontrolerów domeny łączność z Internetem. Z komputerami z uruchomioną usługą Serwer proxy ochrony haseł usługi Azure AD są tylko maszyny wymagające łączności z Internetem.
* Żadne sieci porty są otwarte na kontrolerach domeny.
* Nie zmian schematu usługi Active Directory są wymagane.
   * Oprogramowanie korzysta z istniejącego kontenera usługi Active Directory i obiektów schematu serviceConnectionPoint.
* Nie ma minimalnej domeny usługi Active Directory lub funkcjonalności lasu poziom (DFL\FFL).
* Oprogramowanie nie tworzenie ani nie wymaga żadnych kont w domenach usługi Active Directory, które chroni.
* Wdrożenie przyrostowe jest obsługiwane z zależnościami, czy zasady haseł tylko są wymuszane, którym jest zainstalowany agent kontrolera domeny.
* Ochrony hasłem w usłudze Azure AD nie jest aparat aplikacji zasad w czasie rzeczywistym. Czas między zmiana konfiguracji zasad haseł i czasu osiągnie i są wymuszane na wszystkich kontrolerach domeny może być z opóźnieniem.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises.md)