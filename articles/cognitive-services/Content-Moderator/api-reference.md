---
title: Odwołanie do interfejsu API — moderator zawartości
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o różnych moderacja zawartości i przeglądanie interfejsów API dla moderatora zawartości.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757375"
---
# <a name="content-moderator-api-reference"></a>Informacje o interfejsie API moderatora zawartości

Możesz rozpocząć korzystanie z interfejsów API moderatora zawartości platformy Azure w następujący sposób:

- W witrynie Azure portal [zasubskrybuj interfejs API moderatora zawartości](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Zobacz [Try Content Moderator w internecie,](quick-start.md) aby zarejestrować się za pomocą narzędzia Content Moderator [Review](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>Interfejsy API moderowania

Do skonfigurowania przepływów pracy po moderowaniu można użyć następujących interfejsów API moderatora zawartości.

| Opis | Tematy pomocy |
| -------------------- |-------------|
| **Interfejs API moderowania obrazów**<br /><br />Skanuj obrazy i wykrywaj potencjalne treści dla dorosłych i rasistowskie za pomocą tagów, wyników zaufania i innych wyodrębnionych informacji. <br /><br />Te informacje służy do publikowania, odrzucania lub przeglądania zawartości przepływu pracy po moderowaniu. <br /><br />| [Odwołanie do interfejsu API moderowania obrazu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Odwołanie do interfejsu API moderowania obrazu")   |
| **Interfejs API moderowania tekstu**<br /><br />Skanowanie zawartości tekstowej. Zwroty wulgaryzmów i danych osobowych. <br /><br />Te informacje służy do publikowania, odrzucania lub przeglądania zawartości przepływu pracy po moderowaniu.<br /><br /> | [Odwołanie do interfejsu API moderowania tekstu](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Odwołanie do interfejsu API moderowania tekstu")   |
| **Interfejs API moderowania wideo**<br /><br />Skanuj filmy i wykrywaj potencjalne treści dla dorosłych i rasistowskie. <br /><br />Te informacje służy do publikowania, odrzucania lub przeglądania zawartości przepływu pracy po moderowaniu.<br /><br /> | [Omówienie interfejsu API moderowania wideo](video-moderation-api.md "Omówienie interfejsu API moderowania wideo")   |
| **Interfejs API zarządzania listą**<br /><br />Tworzenie niestandardowych wykluczeń lub list wykluczeń obrazów i tekstu oraz zarządzanie nimi. Jeśli ta opcja jest włączona, operacje **obraz — dopasowywanie** i tekst — operacje **na ekranie** są rozmyte dopasowywania przesłanej zawartości do list niestandardowych. <br /><br />Aby uzyskać wydajność, można pominąć krok moderowania opartego na uczeniu maszynowym.<br /><br /> | [Odwołanie do interfejsu API zarządzania listami](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Odwołanie do interfejsu API zarządzania listami")   |

## <a name="review-apis"></a>Przegląd interfejsów API

Interfejsy API przeglądu mają następujące składniki:

| Opis | Tematy pomocy |
| -------------------- |-------------|
| **Stanowiska**<br /><br /> Inicjowanie przepływów pracy moderowania skanowania i przeglądu zarówno dla zawartości obrazu, jak i tekstu. Zadanie moderowania skanuje zawartość przy użyciu interfejsu API moderowania obrazu i interfejsu API moderowania tekstu. Zadania moderowania używają zdefiniowanych i domyślnych przepływów pracy do generowania recenzji. <br /><br />Po moderatora człowieka przejrzał automatycznie przypisane tagi i dane przewidywania i przesłane decyzji moderowania zawartości, Interfejs API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.<br /><br /> | [Odwołanie do zadania](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Odwołanie do zadania")   |
| **Przeglądy**<br /><br />Użyj narzędzia Recenzja, aby bezpośrednio tworzyć recenzje obrazów lub tekstu dla moderatorów ludzkich.<br /><br /> Po moderatora człowieka przejrzał automatycznie przypisane tagi i dane przewidywania i przesłane decyzji moderowania zawartości, Interfejs API przeglądu przesyła wszystkie informacje do punktu końcowego interfejsu API.<br /><br /> | [Odwołanie do przeglądu](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Odwołanie do przeglądu")   |
| **Przepływy**<br /><br />Tworzenie, aktualizowanie i utrzymyzyj szczegółowe informacje o niestandardowych przepływach pracy tworzone przez zespół. Przepływy pracy można zdefiniować za pomocą narzędzia Recenzja. <br /> <br />Przepływy pracy zazwyczaj używają moderatora zawartości, ale mogą również używać niektórych innych interfejsów API, które są dostępne jako łączniki w narzędziu Recenzja.<br /><br /> | [Odwołanie do przepływu pracy](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Odwołanie do przepływu pracy")   |