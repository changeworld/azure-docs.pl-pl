---
title: 'Wyszukiwanie niestandardowe Bing: Wyszukiwania widoku niestandardowego | Dokumentacja firmy Microsoft'
description: Opisuje sposób wyszukiwania niestandardowy widok sieci web
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 7a2db7881dfe7efedb1a83637a6281f786652958
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46964907"
---
# <a name="call-your-custom-search"></a>Wywołanie usługi wyszukiwania niestandardowego

Przed wprowadzeniem pierwsze wywołanie interfejsu API wyszukiwania niestandardowego można pobrać wyniki wyszukiwania dla swojego wystąpienia, należy uzyskać klucz subskrypcji usług Cognitive Services. Aby uzyskać klucz interfejsu API wyszukiwania niestandardowego, zobacz [spróbuj usług Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).


## <a name="try-it-out"></a>Wypróbuj

Po skonfigurowaniu środowiska wyszukiwania niestandardowego, możesz przetestować konfigurację z poziomu portalu wyszukiwania niestandardowego. 

1. Zaloguj się do [wyszukiwania niestandardowego](https://customsearch.ai).
2. Kliknij wystąpienie wyszukiwania niestandardowego z listy wystąpień.
3. Kliknij przycisk **produkcji** kartę. 
4. W obszarze **punktów końcowych** , a następnie wybierz punkt końcowy (na przykład interfejsu API sieci Web). Subskrypcja Określa, które punkty końcowe są wyświetlane (zobacz [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) opcji subskrypcji). 
5. Określ wartości parametrów. 

    Poniżej przedstawiono możliwe parametry można ustawić (rzeczywistej listy zależy od wybranego punktu końcowego). Aby uzyskać dodatkowe informacje na temat tych parametrów, zobacz [interfejs API wyszukiwania niestandardowego](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) odwołania.

    - **Zapytanie**: wyszukiwany termin do wyszukania. Dostępne tylko w przypadku punktów końcowych sieci Web, obrazów, wideo i automatycznego sugerowania.
    - **Niestandardowy identyfikator konfiguracji**: identyfikator konfiguracji wybranego wystąpienia wyszukiwania niestandardowego. To pole jest tylko do odczytu.
    - **Rynek**: rynku, skąd pochodzą wyniki. Dostępne tylko w przypadku punktów końcowych sieci Web, obrazów, wideo i hostowanych interfejsu użytkownika.
    - **Klucz subskrypcji**: klucz subskrypcji na potrzeby testów. Możesz wybierz klucz z listy rozwijanej lub wprowadzić ręcznie.  
      
    Klikając **dodatkowe parametry** , co spowoduje wyświetlenie następujących parametrów:  
      
    - **Bezpieczne wyszukiwanie**: Filtr używane do filtrowania strony internetowe dla dorosłych. Dostępne tylko dla punktów końcowych sieci Web, obrazów, wideo i hostowanych interfejsu użytkownika.
    - **Język interfejsu użytkownika**: język używany do ciągi interfejsu użytkownika. Na przykład, jeśli zostanie włączone, obrazy i klipy wideo w interfejsie użytkownika usług hostowanych **obraz** i **wideo** karty użyć określonego języka.
    - **Liczba**: liczba wyników wyszukiwania, aby zwrócić w odpowiedzi. Dostępne tylko dla punktów końcowych sieci Web, obrazów i filmów wideo.
    - **Przesunięcie**: liczba wyników wyszukiwania, aby pominąć przed zwróceniem wyników. Dostępne tylko dla punktów końcowych sieci Web, obrazów i filmów wideo.

6. Po określeniu wszystkie wymagane opcje, kliknij przycisk **wywołania** Aby wyświetlić odpowiedź w formacie JSON w okienku po prawej stronie. 

Jeśli wybierzesz hostowanych interfejsu użytkownika punktu końcowego, możesz przetestować środowiska wyszukiwania w dolnym okienku.

## <a name="next-steps"></a>Kolejne kroki

- [Wywoływanie w widoku niestandardowym przy użyciu języka C#](./call-endpoint-csharp.md)
- [Wywoływanie w widoku niestandardowym przy użyciu języka Java](./call-endpoint-java.md)
- [Wywołania z usługą NodeJs w widoku niestandardowym](./call-endpoint-nodejs.md)
- [Wywoływanie w widoku niestandardowym przy użyciu języka Python](./call-endpoint-python.md)

- [Wywoływanie w widoku niestandardowym przy użyciu zestawu SDK języka C#](./sdk-csharp-quick-start.md)