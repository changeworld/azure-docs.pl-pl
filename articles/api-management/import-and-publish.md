---
title: Importowanie i publikowanie pierwszego interfejsu API w usłudze Azure API Management
description: Dowiedz się, jak zaimportować interfejs API specyfikacji OpenAPI do usługi Azure API Management i przetestować interfejs API w witrynie Azure portal.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: apimpm
ms.openlocfilehash: 886063dcf886d79ac960814f20b3789e8e3b6839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78163508"
---
# <a name="import-and-publish-your-first-api"></a>Importowanie i publikowanie pierwszego interfejsu API 

W tym samouczku pokazano, jak zaimportować interfejs API wewnętrznej bazy danych specyfikacji OpenAPI w formacie JSON do usługi Azure API Management. Firma Microsoft udostępnia interfejs API wewnętrznej [https://conferenceapi.azurewebsites.net?format=json](https://conferenceapi.azurewebsites.net?format=json)bazy danych i hostuje go na platformie Azure pod adresem .

Po zaimportowaniu interfejsu API wewnętrznej bazy danych do usługi API Management interfejs API staje się fasadą interfejsu API wewnętrznej bazy danych. Fasadę można dostosować do swoich potrzeb w interfejsie API Management bez dotykania interfejsu API wewnętrznej bazy danych. Aby uzyskać więcej informacji, zobacz [Przekształcanie i ochrona interfejsu API](transform-api.md). 

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie interfejsu API do zarządzania interfejsem API
> * Testowanie interfejsu API w witrynie Azure Portal

![Nowy interfejs API](./media/api-management-import-and-publish/created-api.png)

## <a name="prerequisites"></a>Wymagania wstępne

- Poznaj [terminologię usługi Azure API Management](api-management-terminology.md).
- [Utwórz wystąpienie usługi Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-backend-api"></a><a name="create-api"> </a>Importowanie i publikowanie interfejsu API zaplecza

W tej sekcji pokazano, jak zaimportować i opublikować interfejs API wewnętrznej bazy danych specyfikacji OpenAPI.
 
1. W lewej nawigacji wystąpienia zarządzania interfejsami API wybierz **interfejsy API** z sekcji **Zarządzanie interfejsami API.**
1. Zaznacz kafelek **OpenAPI,** a następnie wybierz pozycję **Pełny** na ekranie podręcznym.
1. Na ekranie **Specyfikacja Create from OpenAPI** użyj wartości z poniższej tabeli, aby utworzyć interfejs API.
   
   Czerwona gwiazdka obok pola w formularzu wskazuje, że pole jest wymagane. Wartości interfejsu API można ustawić podczas tworzenia lub później, przechodząc do karty **Ustawienia.** 
   
   ![Tworzenie interfejsu API](./media/api-management-import-and-publish/create-api.png)
   
   |Ustawienie|Wartość|Opis|
   |-------|-----|-----------|
   |**Specyfikacja OpenAPI**|*https:\//conferenceapi.azurewebsites.net?format=json*|Usługa implementująca interfejs API. Usługa API Management przekazuje żądania na ten adres.|
   |**Nazwa wyświetlana**|Po wprowadzeniu poprzedniego adresu URL usługi usługa API Management wypełnia to pole na podstawie JSON.|Nazwa wyświetlana w portalu dla deweloperów.|
   |**Nazwa**|Po wprowadzeniu poprzedniego adresu URL usługi usługa API Management wypełnia to pole na podstawie JSON.|Unikatowa nazwa interfejsu API.|
   |**Opis**|Po wprowadzeniu poprzedniego adresu URL usługi usługa API Management wypełnia to pole na podstawie JSON.|Opcjonalny opis interfejsu API.|
   |**Schemat adresu URL**|**Protokół HTTPS**|Protokoły, których można używać do uzyskiwania dostępu do interfejsu API.|
   |**Sufiks adresu URL interfejsu API**|*conference*|Sufiks został dołączeniu do podstawowego adresu URL usługi api Management. Usługa API Management odróżnia interfejsy API według ich sufiksu, więc sufiks musi być unikatowy dla każdego interfejsu API dla danego wydawcy.|
   |**Produkty**|**Unlimited (nieograniczony)**|Skojarzenie jednego lub więcej interfejsów API. Każde wystąpienie api management zawiera dwa przykładowe produkty: **Starter** i **Unlimited**. Publikujesz interfejs API, kojarząc interfejs API z produktem **Unlimited** w tym przykładzie.<br/>Możesz dołączyć kilka interfejsów API w produkcie i zaoferować je deweloperom za pośrednictwem portalu dla deweloperów. Aby dodać ten interfejs API do innego produktu, wpisz lub wybierz nazwę produktu. Powtórz ten krok, aby dodać interfejs API do wielu produktów. Interfejsy API można również dodać do produktów później na stronie **Ustawienia.**<br/>Przed uzyskaniem dostępu do interfejsu API deweloperzy muszą najpierw zasubskrybować produkt. Gdy subskrybują, otrzymują klucz subskrypcji, który jest dobry dla dowolnego interfejsu API w tym produkcie. <br/>Jeśli utworzono wystąpienie usługi API Management, jesteś już administratorem, więc subskrybujesz każdy produkt w wystąpieniu.|
   |**Tagi**| |Tagi do organizowania interfejsów API do wyszukiwania, grupowania lub filtrowania.|
   |**Czy chcesz utworzyć wersję tego interfejsu API?**|Zaznaczanie lub usuwanie zaznaczania|Aby uzyskać więcej informacji na temat przechowywania wersji, zobacz [Publikowanie wielu wersji interfejsu API](api-management-get-started-publish-versions.md).|
   
   > [!NOTE]
   > Aby opublikować interfejs API, musisz skojarzyć go z produktem. Można to zrobić na stronie **Ustawienia.**
   
1. Wybierz **pozycję Utwórz**.

Jeśli masz problemy z importowaniem definicji interfejsu API, zobacz [listę znanych problemów i ograniczeń](api-management-api-import-restrictions.md).

## <a name="test-the-new-api-in-the-azure-portal"></a>Testowanie nowego interfejsu API w witrynie Azure portal

Operacje interfejsu API można wywołać bezpośrednio z witryny Azure portal, która zapewnia wygodny sposób wyświetlania i testowania operacji.

1. W lewej nawigacji wystąpienia zarządzania interfejsami API wybierz pozycję **INTERFEJSY API** z sekcji **Zarządzanie interfejsami API,** a następnie wybierz pozycję **Demo Conference API**.
1. Wybierz kartę **Test,** a następnie wybierz pozycję **Pobierzujpekarze**. Strona zawiera **parametry kwerendy** i **nagłówki**, jeśli istnieją. **Klucz subskrypcji Ocp-Apim-Subscription** jest wypełniany automatycznie dla klucza subskrypcji skojarzonego z tym interfejsem API.
1. Wybierz pozycję **Wyślij**.
   
   ![Mapa testowania interfejsu API](./media/api-management-import-and-publish/01-import-first-api-01.png)
   
   Zaplecze odpowiada wartością **200 OK** i pewnymi danymi.

## <a name="next-steps"></a><a name="next-steps"> </a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Importowanie pierwszego interfejsu API
> * Testowanie interfejsu API w witrynie Azure Portal

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć i opublikować produkt:

> [!div class="nextstepaction"]
> [Tworzenie i publikowanie produktu](api-management-howto-add-products.md)
