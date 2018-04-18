---
title: 'Azure Active Directory B2C: Siedziby dostępności & dane regionu | Dokumentacja firmy Microsoft'
description: Temat dotyczący typów dzierżaw usługi Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/10/2017
ms.author: davidmu
ms.openlocfilehash: b982a6b6e0068370ef7b5cb30ea689829e90d494
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Siedziby dostępności & dane regionu
Dostępność w danym regionie i siedziby danych są dwa bardzo różnych zagadnienia dotyczące inaczej usługi Azure AD B2C od pozostałej części Azure. W tym artykule wyjaśniono różnice między tych dwóch pojęć, a następnie porównaj sposobów ich zastosowania do platformy Azure i usługi Azure AD B2C.

## <a name="summary"></a>Podsumowanie
Usługa Azure AD B2C jest **ogólnie dostępna na całym świecie** z opcją dla **danych siedzibę w Stanach Zjednoczonych lub w Europie**.

## <a name="concepts"></a>Pojęcia
* **Dostępność w danym regionie** odwołuje się do kiedy usługa jest dostępna do użycia.
* **Siedziby danych** odwołuje się do przechowywania danych użytkownika.

## <a name="region-availability"></a>Dostępność w danym regionie
Usługa Azure AD B2C jest dostępna na całym świecie za pośrednictwem chmury publicznej platformy Azure. 

To różni się od modelu wykonaj większości innych usług platformy Azure, której połączenie z siedziby danych dostępności. Zawiera przykłady tego, w obu Azure [produktów dostępna w regionie](https://azure.microsoft.com/regions/services/) strony i [Kalkulator cen Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Rezydencja danych
Usługa Azure AD B2C są przechowywane dane użytkownika w Stanach Zjednoczonych lub w Europie.

Siedziby danych jest określana według kraju/regionu, którego wybrano po [tworzenie dzierżawy usługi Azure AD B2C](active-directory-b2c-get-started.md).

![Zrzut ekranu przedstawiający dzierżawy w wersji zapoznawczej](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

Dane znajdują się w Stanach Zjednoczonych dla następujących krajach/regionach:

> Stany Zjednoczone, Kanada, Kostaryka, Republika Dominikańska, Salwador, Gwatemala, Meksyk, Panama, Portoryko i Trynidad

Dane znajdują się w Europie dla następujących krajach/regionach:

> Algierii, Austrii, Azerbejdżanu, Bahrajn, Białorusi, Belgii, Bułgarii, Chorwacji, Cypru, Czechy, Dania, Egiptu, Estonii, Finlandia, Francja, Niemcy, Grecja, Węgierskiej, Islandii, Irlandii, Izrael, Włochy, Jordania, Kazachstanu, Kenii, Kuwejt, Lativa, Liban, Liechtenstein, Lituania, Luksemburg, Macedonia BJR, m Alta, Czarnogóry, Maroka, Holandia, Nigerii, Norwegia, Oman, Pakistanu, Polski, Portugalia, Katar, Rumunii, Rosji, Arabia Saudyjska, Serbii, Słowacji, Słowenii, Republika Południowej Afryki, Hiszpanii, Szwecja, Szwajcarii, Tunezji, Turcja, Ukrainy, Zjednoczone Emiraty Arabskie i Zjednoczone Królestwo.

Pozostałe krajach/regionach są właśnie dodawany do listy.  Obecnie można nadal używać usługi Azure AD B2C, wybierając żadnego krajach/regionach powyżej.

> Afganistanu, Argentyny, Australii, Brazylia, podrzędnej lokacji, Kolumbii, Ekwadoru, Hongkong SAR, Indie, Indonezji, Iraku, Japonii, Korei, Malezji, Nowa Zelandia, Paragwaju, Peru, Filipiny, Singapur, Sri Lanka, Tajwan, Tajlandii, Urugwaju i Wenezueli.

## <a name="preview-tenant"></a>Podgląd dzierżawy
Jeśli dzierżawy B2C została utworzona w okresie wersji zapoznawczej usługi Azure AD B2C, istnieje duże prawdopodobieństwo, że Twoje **dzierżawy typu** mówi **dzierżawy w wersji zapoznawczej**. Jeśli jest to możliwe, musi użyć dzierżawy tylko w przypadku projektowania i testowania, a nie dla aplikacji w środowisku produkcyjnym.

> [!IMPORTANT]
> Brak nie ścieżki migracji z dzierżawy B2C w wersji zapoznawczej do dzierżawy B2C skali produkcji. Należy pamiętać, że istnieją znane problemy podczas usuwania dzierżawy B2C w wersji zapoznawczej i ponownie utwórz dzierżawy B2C produkcji skali, z tą samą nazwą domeny. Należy utworzyć dzierżawy B2C skali produkcji o nazwie innej domeny.


![Zrzut ekranu przedstawiający dzierżawy w wersji zapoznawczej](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)
