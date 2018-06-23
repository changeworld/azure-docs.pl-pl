---
title: Dostosowywanie tłumaczenie tekstu interfejsu API Microsoft Translator | Dokumentacja firmy Microsoft
description: Centrum firmy Microsoft Translator umożliwia tworzenie własnych systemu tłumaczenia maszynowego przy użyciu terminologii preferowanych i styl.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: 1db22a414c41f338c4e7fd6ce9dc7ac739fa9237
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349261"
---
# <a name="customize-your-text-translations"></a>Dostosowywanie tłumaczenia tekstu

Podgląd Microsoft niestandardowe Translator to funkcja usługi Microsoft Translator, która umożliwia użytkownikom dostosowywanie zaawansowane neuronowej tłumaczenia maszynowego Microsoft Translator podczas tłumaczenia tekstu za pomocą API Microsoft Translator tekst (tylko wersja 3). 

Tę funkcję można również dostosować mowy tłumaczenia, gdy jest używany z [kognitywnych mowy usługi Podgląd](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Translator niestandardowych
Z Translator niestandardowe można tworzyć neuronowej tłumaczenia systemów, które informacje z terminologią używaną w własne biznesowych oraz z branży. Następnie zintegruje systemu tłumaczenia dostosowane do istniejących aplikacji, przepływy pracy i witryn sieci Web. 

### <a name="how-does-it-work"></a>Jak to działa?
Użyj wcześniej przetłumaczonego dokumentów (ulotki, stron sieci Web, dokumentacji, itp.), systemu tłumaczenia, odzwierciedlający styl i terminologia specyficznego dla domeny, na których lepiej niż system ogólnego tłumaczenia kompilacji. Użytkownicy mogą przekazywać dokumenty TMX, XLIFF TXT, DOCX i XLSX.  

System również akceptuje dane, które jest równoległego na poziomie dokumentu, ale jeszcze nie jest wyrównany na poziomie zdania. Jeśli użytkownicy mają dostęp do wersji tej samej zawartości, w wielu językach, ale w oddzielnych dokumentów Translator niestandardowe będzie automatycznie dopasować zdań w dokumentach.  System umożliwia również jednojęzyczne danych w jedną lub obie te języki uzupełniają dane szkoleniowe równoległych zwiększające tłumaczenia. 

Dostosowany system jest dostępna za pomocą regularnego wywołania do interfejsu API Microsoft Translator tekstu przy użyciu parametru kategorii.

Podany odpowiedniego typu i ilość danych szkoleniowych nie jest rzadko oczekiwane korzyści w zakresie od 5 do 10 lub BELEU jeszcze więcej punktów jakości tłumaczenia przy użyciu translatora niestandardowe.

Więcej informacji na temat różnych poziomów dostosowywania oparte na dostępnych danych znajdują się w [Podręcznik użytkownika Translator niestandardowe](http://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Centrum Microsoft Translator

Centrum starszych Translator firmy Microsoft może służyć do tłumaczenia statystyczne tłumaczenia maszynowego. [Dowiedz się więcej](https://www.microsoft.com/en-us/translator/hub.aspx) 

## <a name="custom-translator-versus-hub"></a>Translator niestandardowych i Centrum

|   | **Centrum** | **Translator niestandardowych**|
|:-----|:----:|:----:|
|Stan funkcji dostosowywania   | Ogólna dostępność  | Wersja zapoznawcza |
| Wersja interfejsu API tekstu  | Tylko w wersji 2   | Tylko w wersji 3 |
| Dostosowywanie SMT | Yes   | Nie | 
| Dostosowywanie NMT | Nie    | Yes |
| Nowe ujednolicone dostosowywanie usług mowy | Nie    | Yes | 
| [Nie śledzenia](http://www.aka.ms/notrace) | Yes   | Yes | 

## <a name="collaborative-translations-framework"></a>Framework współpracy tłumaczenia

> [!NOTE]
> Począwszy od 1 lutego 2018 AddTranslation() i AddTranslationArray() nie są już dostępne do użycia z Translator tekst interfejsu API w wersji 2.0. Te metody zakończy się niepowodzeniem i nic nie zostanie zapisany. Translator tekst interfejsu API w wersji 3.0 nie obsługuje tych metod.

>Podobne funkcje są dostępne w interfejsie API Translator koncentratora. Zobacz [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger). 

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Skonfigurowanie systemu dostosowane języka, przy użyciu translatora niestandardowe](http://aka.ms/CustomTranslatorDocs)
