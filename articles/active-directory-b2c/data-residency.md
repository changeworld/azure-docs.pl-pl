---
title: Dostępność regionu i miejsce zamieszkania danych
titleSuffix: Azure AD B2C
description: Dostępność regionu, miejsce zamieszkania danych i informacje o dzierżawach usługi Azure Active Directory B2C w wersji zapoznawczej.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3df0f581d0d2a1e5ca02202b4eeaede5a1dd5362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188854"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Usługa Azure Active Directory B2C: dostępność regionu & rezydencji danych

Dostępność regionu i rezydencji danych to dwie bardzo różne koncepcje, które mają różny zakres usług Azure AD B2C niż reszta platformy Azure. W tym artykule wyjaśniono różnice między tymi dwoma pojęciami i porównuje sposób ich stosowania do platformy Azure w porównaniu do usługi Azure AD B2C.

Usługa Azure AD B2C jest **ogólnie dostępna na całym świecie** z opcją **rezydencji danych** w **Stanach Zjednoczonych, Europie lub regionie Azji i Pacyfiku.**

[Dostępność regionu](#region-availability) odnosi się do miejsca, w którym usługa jest dostępna do użytku.

[Data residency](#data-residency) odnosi się do miejsca przechowywania danych użytkownika.

## <a name="region-availability"></a>Dostępność w danym regionie

Usługa Azure AD B2C jest dostępna na całym świecie za pośrednictwem chmury publicznej platformy Azure.

Różni się to od modelu, po którym następuje większość innych usług platformy Azure, które zazwyczaj *paruje dostępność* z *rezydentem danych.* Przykłady tego można zobaczyć zarówno na stronie [Produkty dostępne dla regionu](https://azure.microsoft.com/regions/services/) platformy Azure, jak i w [kalkulatorze cen B2C usługi Active Directory.](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

## <a name="data-residency"></a>Rezydencja danych

Usługa Azure AD B2C przechowuje dane użytkowników w Stanach Zjednoczonych, Europie lub regionie Azji i Pacyfiku.

Miejsce zamieszkania danych zależy od kraju/regionu wybranego podczas [tworzenia dzierżawy usługi Azure AD B2C:](tutorial-create-tenant.md)

![Zrzut ekranu przedstawiający dzierżawę w wersji zapoznawczej](./media/data-residency/data-residency-b2c-tenant.png)

Dane znajdują się w **Stanach Zjednoczonych** dla następujących krajów/regionów:

> Stany Zjednoczone, Kanada, Kostaryka, Dominikana, Salwador, Gwatemala, Meksyk, Panama, Portoryko i Trynidad & Tobago

Dane znajdują się w **Europie** dla następujących krajów/regionów:

> Algieria, Austria, Azerbejdżan, Bahrajn, Białoruś, Belgia, Bułgaria, Chorwacja, Cypr, Czechy, Dania, Egipt, Estonia, Finlandia, Francja, Niemcy, Grecja, Węgry, Islandia, Irlandia, Izrael, Włochy, Jordania, Kazachstan, Kenia, Kuwejt, Łotwa, Liban, Liechtenstein, Litwa, Luksemburg, Macedonia Północna, Malta, Czarnogóra, Maroko, Holandia, Nigeria, Norwegia, Oman, Pakistan, Polska, Portugalia, Katar, Rumunia, Rosja, Arabia Saudyjska, Serbia, Słowacja, Słowenia, Republika Południowej Afryki, Hiszpania, Szwecja, Szwajcaria, Tunezja, Turcja, Ukraina, Zjednoczone Emiraty Arabskie i Wielka Brytania.

Dane znajdują się w **regionie Azji i Pacyfiku** dla następujących krajów/regionów:

> Afganistan, Hongkong SAR, Indie, Indonezja, Japonia, Korea, Malezja, Filipiny, Singapur, Sri Lanka, Tajwan i Tajlandia.

Następujące kraje/regiony są w trakcie dodawania do listy. Na razie nadal można używać usługi Azure AD B2C, wybierając dowolny z krajów/regionów powyżej.

> Argentyna, Australia, Brazylia, Chile, Kolumbia, Ekwador, Irak, Nowa Zelandia, Paragwaj, Peru, Urugwaj i Wenezuela.

## <a name="preview-tenant"></a>Dzierżawa w wersji zapoznawczej

Jeśli utworzono dzierżawę B2C w okresie podglądu usługi Azure AD B2C, prawdopodobnie **typ dzierżawy** będzie wyświetlany **jako dzierżawa w wersji zapoznawczej.**

W takim przypadku należy użyć dzierżawy TYLKO do celów rozwoju i testowania. NIE używaj dzierżawy w wersji zapoznawczej dla aplikacji produkcyjnych.

**Nie ma ścieżki migracji** z dzierżawy B2C w wersji zapoznawczej do dzierżawy B2C w skali produkcyjnej. Należy utworzyć nową dzierżawę B2C dla aplikacji produkcyjnych.

Istnieją znane problemy podczas usuwania dzierżawy B2C w wersji zapoznawczej i tworzenia dzierżawy B2C w skali produkcyjnej o tej samej nazwie domeny. *Należy utworzyć dzierżawę B2C w skali produkcyjnej o innej nazwie domeny.*

![Zrzut ekranu przedstawiający dzierżawę w wersji zapoznawczej](./media/data-residency/preview-b2c-tenant.png)