---
title: Usługa Azure AD hasło protection w wersji zapoznawczej
description: Zablokuj słabe hasła w Active Directory w środowisku lokalnym za pomocą hasła usługi Azure AD protection (wersja zapoznawcza)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 816c459ca6edd7204ccdcdf9d402f2d4499d9116
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2019
ms.locfileid: "55662527"
---
# <a name="preview-enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Wersja zapoznawcza: Wymuszanie ochrona za pomocą hasła usługi Azure AD dla usługi Active Directory systemu Windows Server

|     |
| --- |
| Ochrony hasłem w usłudze Azure AD i listy niestandardowej zakazanych haseł są publicznej wersji zapoznawczej funkcji usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowym warunkom użytkowania wersji zapoznawczych usług Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Ochrony hasłem w usłudze Azure AD to nowa funkcja w wersji zapoznawczej obsługiwane przez usługi Azure Active Directory (Azure AD), aby zwiększyć zasady haseł w organizacji. Wdrażanie w środowisku lokalnym ochrona za pomocą hasła usługi Azure AD używa zarówno globalnych i niestandardowe zakazane listy haseł przechowywanych w usłudze Azure AD i wykonuje ten sam kontroli lokalnej zgodnie ze zmianami oparte na chmurze usługi Azure AD.

Istnieją trzy składniki oprogramowania, które tworzą ochrona za pomocą hasła usługi Azure AD:

* Usługa serwera proxy ochrony haseł usługi Azure AD działa na dowolnym komputerze przyłączonym do domeny w bieżącym lesie usługi Active Directory. Jego przekazuje żądania z kontrolerów domeny do usługi Azure AD i zwraca odpowiedź z usługi Azure AD do kontrolera domeny.
* Usługa agenta ochrony kontrolera domeny hasła usługi Azure AD odbiera żądania weryfikacji hasła z dll filtru haseł agenta kontrolera domeny, przetwarza je za pomocą bieżące zasady haseł dostępne lokalnie i zwraca wynik (pass\fail). Ta usługa jest odpowiedzialna za okresowo (raz na godzinę) podczas wywoływania usługi Azure AD hasło ochrony serwera proxy do pobierania nowych wersji zasad haseł. Komunikacja między usługi agenta ochrony kontrolera domeny hasła usługi Azure AD i usługi Azure AD hasło ochrony serwera proxy odbywa się przy użyciu RPC (zdalne wywoływanie procedury) za pośrednictwem protokołu TCP. Podczas pobierania nowe zasady są przechowywane w folderze sysvol, gdzie można replikować na innych kontrolerach domeny. Usługa agenta DC monitoruje również folder sysvol dla zmian w przypadku, gdy inne kontrolery domeny zostały napisane nowych zasad haseł Jeśli dostępne są już odpowiednio najnowsze zasady, nowe żądania pobierania zasad zostanie pominięte.
* Dll filtru haseł agenta DC odbiera żądania weryfikacji hasła od systemu operacyjnego i przekazuje je do usługi agenta ochrony kontrolera domeny hasła usługi Azure AD działa lokalnie na kontrolerze domeny.

![W jaki sposób składniki ochrony hasła usługi Azure AD współpracują ze sobą](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

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
* Zalecane jest, aby zainstalować agenta kontrolera domeny na wszystkie kontrolery domeny, aby upewnić się, wymuszanie ochrony hasła.
* Ochrony hasłem w usłudze Azure AD nie jest aparat aplikacji zasad w czasie rzeczywistym. Czas między zmiana konfiguracji zasad haseł i czasu osiągnie i są wymuszane na wszystkich kontrolerach domeny może być z opóźnieniem.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)
