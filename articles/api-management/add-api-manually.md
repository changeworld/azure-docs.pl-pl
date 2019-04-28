---
title: Ręczne dodawanie interfejsu API przy użyciu witryny Azure Portal | Microsoft Docs
description: Ten samouczek przedstawia sposób użycia usługi API Management (APIM) do ręcznego dodawania interfejsu API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2018
ms.author: apimpm
ms.openlocfilehash: 14d4bf6d7e1d1f474e2388c4e2ce232574ebf0d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60798506"
---
# <a name="add-an-api-manually"></a>Ręczne dodawanie interfejsu API

W tym artykule opisano kroki ręczne dodawanie interfejsu API do wystąpienia usługi API Management (APIM) przy użyciu witryny Azure portal. Typowy scenariusz, w przypadku którego tworzysz pusty interfejs API i definiujesz go ręcznie, to pozorowanie interfejsu API. Aby uzyskać szczegółowe informacje na interfejsu API na temat pozorowania interfejsu API, zobacz [Pozorowanie odpowiedzi interfejsu API](mock-api-responses.md).

Jeśli chcesz zaimportować istniejący interfejs API, zobacz sekcję [tematów pokrewnych](#related-topics).

W tym artykule tworzymy pusty interfejs API i określamy usługę [httpbin.org](https://httpbin.org) (publiczną usługę testowania) jako interfejs API zaplecza.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Tworzenie interfejsu API

1. Wybierz pozycję **Interfejsy API** w obszarze **API MANAGEMENT**.
2. Z menu po lewej stronie wybierz pozycję **+ Dodaj interfejs API**.
3. Z listy wybierz pozycję **Pusty interfejs API**.

    ![Pusty interfejs API](media/add-api-manually/blank-api.png)
4. Wprowadź ustawienia interfejsu API.

    ![Ustawienia](media/add-api-manually/settings.png)

    |**Nazwa**|**Wartość**|**Opis**|
    |---|---|---|
    |**Nazwa wyświetlana**|„*Pusty interfejs API*” |Ta nazwa jest wyświetlana w portalu dla deweloperów.|
    |**Adres URL usługi internetowej** (opcjonalnie)| „*https://httpbin.org*”| Jeśli chcesz pozorować interfejs API, możesz nie wprowadzać żadnych informacji. <br/>W tym przypadku wprowadzamy [https://httpbin.org](https://httpbin.org). Jest to publiczna usługa testowania. <br/>Jeśli chcesz zaimportować interfejs API, który jest automatycznie mapowany na zaplecze, zapoznaj się z jednym z tematów w sekcji [tematów pokrewnych](#related-topics).|
    |**Schemat adresu URL**|„*HTTPS*”|W takim przypadku nawet jeśli zaplecze ma niezabezpieczony dostęp przez protokół HTTP, określamy bezpieczny dostęp usługi APIM przez protokół HTTPS do zaplecza. <br/>Taki scenariusz (HTTPS do HTTP) jest nazywany zakończeniem połączenia HTTPS. Tę czynność można wykonać, jeśli w sieci wirtualnej istnieje interfejs API (jeśli wiadomo, że dostęp jest zabezpieczony, nawet jeśli protokół HTTPS nie jest używany). <br/>Procesu „zakończenia połączenia HTTPS” można użyć, aby zaoszczędzić czas w przypadku niektórych cykli procesora.|
    |**Sufiks adresu URL**|„*hbin*”| Sufiks to nazwa, która identyfikuje dany interfejs API w tym wystąpieniu usługi APIM. Musi ona być unikatowa w tym wystąpieniu usługi APIM.|
    |**Produkty**|„*Nieograniczony*” |Opublikuj interfejs API przez skojarzenie go z produktem. Jeśli chcesz, aby interfejs API został opublikowany i był dostępny dla deweloperów, dodaj go do produktu. Możesz to zrobić podczas tworzenia interfejsu API lub ustawić tę opcję później.<br/><br/>Produkty to skojarzenia co najmniej jednego interfejsu API. Możesz uwzględnić wiele interfejsów API i zaoferować je deweloperom za pośrednictwem portalu deweloperów. <br/>Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Podczas subskrybowania otrzymują oni klucz subskrypcji działający dla każdego interfejsu API w tym produkcie. Jeśli utworzono wystąpienie usługi APIM, oznacza to, że użytkownik jest już administratorem, więc domyślnie posiada subskrypcję każdego produktu.<br/><br/> Domyślnie każde wystąpienie usługi API Management zawiera dwa produkty przykładowe: **początkowy** i **bez ograniczeń**.| 
5. Wybierz pozycję **Utwórz**.

W tym momencie w usłudze APIM nie ma żadnych operacji mapowanych na operacje w interfejsie API zaplecza. Jeśli wywołasz operację, która jest uwidaczniana za pośrednictwem zaplecza, a nie usługi APIM, może wystąpić błąd **404**.

>[!NOTE] 
> Domyślnie po dodaniu interfejsu API, nawet jeśli został on połączony z usługą zaplecza, usługa APIM nie uwidoczni żadnych operacji do momentu umieszczenia ich na liście dozwolonych. Aby umieścić operację usługi zaplecza na liście dozwolonych, utwórz operację APIM mapowaną na operację zaplecza.

## <a name="add-and-test-an-operation"></a>Dodawanie i testowanie operacji

W tej sekcji przedstawiono sposób dodawania operacji „/ get” w celu mapowania jej na operację „http://httpbin.org/get” zaplecza.

### <a name="add-an-operation"></a>Dodawanie operacji

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Kliknij przycisk **+ Dodaj operację**.
3. W polu **adresu URL** wybierz pozycję **GET**, a następnie wprowadź ciąg „*/get*” w zasobie.
4. Wprowadź ciąg „*FetchData*” w polu **Nazwa wyświetlana**.
5. Wybierz pozycję **Zapisz**.

### <a name="test-an-operation"></a>Testowanie operacji

Przetestuj operację w witrynie Azure Portal. Możesz również przetestować ją w **portalu deweloperów**.

1. Wybierz kartę **Test**.
2. Wybierz pozycję **FetchData**.
3. Kliknij pozycję **Wyślij**.

Zostanie wyświetlona odpowiedź generowana przez operację „http://httpbin.org/get”. Jeśli chcesz przekształcać operacje, zobacz temat [Przekształcanie i ochrona interfejsu API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Dodawanie i testowanie operacji sparametryzowanej

W tej sekcji przedstawiono sposób dodawania operacji przyjmującej parametr. W tym przypadku mapujemy operację na adres „http://httpbin.org/status/200”.

### <a name="add-the-operation"></a>Dodawanie operacji

1. Wybierz interfejs API utworzony w poprzednim kroku.
2. Kliknij przycisk **+ Dodaj operację**.
3. W polu **adresu URL** wybierz pozycję **GET**, a następnie wprowadź ciąg „*/status/{code}*” w zasobie. Opcjonalnie możesz podać niektóre informacje skojarzone z tym parametrem. Na przykład wprowadź wartość „*Numer*” w obszarze **TYP** i „*200*” (wartość domyślna) w obszarze **WARTOŚCI**.
4. Wprowadź ciąg „GetStatus” w polu **Nazwa wyświetlana**.
5. Wybierz pozycję **Zapisz**.

### <a name="test-the-operation"></a>Testowanie operacji 

Przetestuj operację w witrynie Azure Portal.  Możesz również przetestować ją w **portalu deweloperów**.

1. Wybierz kartę **Test**.
2. Wybierz pozycję **GetStatus**. Domyślnie wartość kodu jest ustawiana na „*200*”. Można ją zmienić, aby testować inne wartości. Na przykład wpisz wartość „*418*”.
3. Kliknij pozycję **Wyślij**.

    Zostanie wyświetlona odpowiedź generowana przez operację „http://httpbin.org/status/200”. Jeśli chcesz przekształcać operacje, zobacz temat [Przekształcanie i ochrona interfejsu API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Przekształcanie i ochrona opublikowanego interfejsu API](transform-api.md)
