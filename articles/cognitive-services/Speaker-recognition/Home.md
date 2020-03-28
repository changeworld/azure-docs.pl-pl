---
title: Co to jest interfejs API rozpoznawania osoby mówiącej?
titleSuffix: Azure Cognitive Services
description: Weryfikacja prelegenta i identyfikacja prelegentów za pomocą interfejsu API rozpoznawania głośników w usługach Cognitive Services.
services: cognitive-services
author: dwlin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speaker-recognition
ms.topic: overview
ms.date: 10/01/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: f354793e3779f4d9f7be9bae8a21545a15ed1c4c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73464919"
---
# <a name="speaker-recognition-api---preview"></a>Interfejs API rozpoznawania głośników — wersja zapoznawcza

Interfejsy API rozpoznawania głośników to oparte na chmurze interfejsy API, które zapewniają zaawansowane algorytmy sztucznej inteligencji do weryfikacji głośników i identyfikacji głośników. Rozpoznawanie głośników jest podzielone na dwie kategorie: weryfikacja głośników i identyfikacja prelegentów.

## <a name="speaker-verification"></a>Weryfikacja osoby mówiącej

Głos ma unikalne cechy, które mogą być związane z jednostką.  Aplikacje mogą używać głosu jako dodatkowego czynnika weryfikacji w scenariuszach, takich jak centra obsługi i usługi sieci web.

Interfejsy API weryfikacji prelegentów służą jako inteligentne narzędzie ułatwiające weryfikację użytkowników przy użyciu zarówno ich haseł głosowych, jak i mowy.

### <a name="enrollment"></a>Rejestracja

Rejestracja do weryfikacji prelegenta jest zależna od tekstu, co oznacza, że prelegenci muszą wybrać określone hasło do użycia zarówno podczas fazy rejestracji, jak i weryfikacji.

W fazie rejestracji prelegenta głos prelegenta jest nagrywany, wypowiadając określoną frazę. Funkcje głosowe są wyodrębniane w celu utworzenia unikatowego podpisu głosowego podczas rozpoznawanie wybranej frazy. Razem te dane rejestracji prelegenta będą używane do weryfikacji głośnika. Dane rejestracji prelegenta są przechowywane w zabezpieczonym systemie. Klient kontroluje, jak długo powinien być zachowany. Klienci mogą tworzyć, aktualizować i usuwać dane rejestracji dla poszczególnych prelegentów za pośrednictwem wywołań interfejsu API.  Po usunięciu subskrypcji wszystkie dane rejestracji prelegenta skojarzone z subskrypcją również zostaną usunięte.

Klienci powinni upewnić się, że otrzymali odpowiednie uprawnienia od użytkowników do weryfikacji głośników.

### <a name="verification"></a>Weryfikacja

W fazie weryfikacji Klient powinien zadzwonić do interfejsu API weryfikacji głośników z identyfikatorem powiązanym z osobą fizyczną, która ma zostać zweryfikowana.  Usługa wyodrębnia funkcje głosowe i hasło z zapisu mowy wejściowej. Następnie porównuje funkcje z odpowiednimi elementami danych rejestracji prelegenta dla głośnika, którego Klient stara się zweryfikować i określić każde dopasowanie.  Odpowiedź zwraca "accept" lub "reject" z różnymi poziomami ufności.  Klient następnie określa, jak korzystać z wyników, aby pomóc zdecydować, czy ta osoba jest zarejestrowany mówca.

Próg ufności należy ustalić na podstawie scenariusza i innych czynników weryfikacji, które są używane. Zaleca się eksperymentować z poziomem ufności i należy wziąć pod uwagę odpowiednie ustawienie dla każdej aplikacji. Interfejsy API nie są przeznaczone do określenia, czy dźwięk pochodzi od osoby aktywnej, czy imitacji lub nagrania zarejestrowanego głośnika.

Usługa nie zachowuje nagrywania mowy ani wyodrębnionych funkcji głosowych, które są wysyłane do usługi podczas fazy weryfikacji.

Aby uzyskać więcej informacji na temat weryfikacji osoby mówiącej, zapoznaj się z artykułem dotyczącym interfejsu API [weryfikacji osoby mówiącej](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652).

## <a name="speaker-identification"></a>Identyfikacja osoby mówiącej

Aplikacje mogą używać głosu do identyfikowania "kto mówi", biorąc pod uwagę grupę zarejestrowanych mówców. Interfejsy API identyfikacji prelegenta mogą być używane w scenariuszach, takich jak wydajność spotkania, personalizacja i transkrypcja call center.

### <a name="enrollment"></a>Rejestracja

Rejestracja w celu identyfikacji osoby mówiącej jest niezależna od tekstu, co oznacza, że nie ma żadnych ograniczeń w zakresie słów wypowiadanych przez osobę mówiącą na nagraniu dźwiękowym. Hasło nie jest wymagane.

W fazie rejestracji głos prelegenta jest nagrywany, a funkcje głosowe są wyodrębniane w celu utworzenia unikatowego podpisu głosowego. Wyodrębniony dźwięk mowy i funkcje są przechowywane w zabezpieczonym systemie. Klient kontroluje, jak długo jest zachowywany. Klienci mogą tworzyć, aktualizować i usuwać te dane rejestracji prelegentów dla poszczególnych głośników za pośrednictwem wywołań interfejsu API. Po usunięciu subskrypcji wszystkie dane rejestracji prelegenta skojarzone z subskrypcją również zostaną usunięte.

Klienci powinni upewnić się, że otrzymali odpowiednie uprawnienia od użytkowników do identyfikacji prelegenta.

### <a name="identification"></a>Identyfikacja

W fazie identyfikacji usługa identyfikacji głośników wyodrębnia funkcje głosowe z wejściowego zapisu mowy. Następnie porównuje funkcje z danymi rejestracyjnymi określonej listy głośników. Po znalezieniu dopasowania z zarejestrowanym mówcą odpowiedź zwraca identyfikator głośnika z poziomem ufności.  W przeciwnym razie odpowiedź zwraca "odrzuć", gdy żaden głośnik nie jest zgodny z zarejestrowanym mówcą.

Próg ufności należy ustawić na podstawie scenariusza. Zaleca się eksperymentować z poziomem ufności i należy wziąć pod uwagę odpowiednie ustawienie dla każdej aplikacji. Interfejsy API nie są przeznaczone do określenia, czy dźwięk pochodzi od osoby aktywnej, czy imitacji lub nagrania zarejestrowanego głośnika.

Usługa nie zachowuje nagrywania mowy ani wyodrębnionych funkcji głosowych, które są wysyłane do usługi w celu fazy identyfikacji.

Aby uzyskać więcej informacji na temat identyfikacji osoby mówiącej, zapoznaj się z artykułem dotyczącym interfejsu API  [identyfikacji osoby mówiącej](https://westus.dev.cognitive.microsoft.com/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e).
