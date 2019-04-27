---
title: Tworzenie wystąpienia usługi Azure API Management | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby utworzyć nowe wystąpienie usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cflower
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: ef874e5d773e87963b6de8371986ac2196fc38f3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558265"
---
# <a name="create-a-new-azure-api-management-service-instance"></a>Tworzenie nowego wystąpienia usługi Azure API Management

Usługa Azure API Management (APIM) pomaga organizacjom publikować interfejsy API dla deweloperów zewnętrznych, partnerskich i wewnętrznych, aby w pełni wykorzystać potencjał danych i usług. Usługa API Management udostępnia podstawowe funkcje wymagane do tworzenia skutecznych interfejsów API przez zaangażowanych deweloperów, a także zapewnia informacje biznesowe, analizy, zabezpieczenia i ochronę. Usługa APIM pozwala tworzyć nowoczesne bramy interfejsów API dla istniejących usług zaplecza hostowanych w dowolnym miejscu oraz zarządzać tymi bramami. Aby uzyskać więcej informacji, zobacz temat [Omówienie](api-management-key-concepts.md).

W tym przewodniku Szybki start opisano procedurę tworzenia nowego wystąpienia usługi API Management za pomocą witryny Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

![nowe wystąpienie](./media/get-started-create-service-instance/get-started-create-service-instance-created.png)

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Zaloguj się do witryny Azure Portal na stronie https://portal.azure.com.

## <a name="create-a-new-service"></a>Tworzenie nowej usługi

![Nowe wystąpienie usługi Azure API Management](./media/get-started-create-service-instance/00-CreateResource-01.png)

1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz pozycję **Utwórz zasób** > **Integracja dla przedsiębiorstw** > **API Management**.

    Możesz też wybrać pozycję **Nowy**, wpisać `API management` w polu wyszukiwania i nacisnąć klawisz Enter. Kliknij pozycję **Utwórz**.

2. Wprowadź ustawienia w oknie **Usługa API Management**.

    ![nowe wystąpienie](./media/get-started-create-service-instance/get-started-create-service-instance-create-new.png)

    | Ustawienie                 | Sugerowana wartość                               | Opis                                                                                                                                                                                                                                                                                                                         |
|-------------------------|-----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nazwa**                | Unikatowa nazwa dla usługi API Management | Nazwy nie można później zmienić. Nazwa usługi jest używana do generowania domyślnej nazwy domeny w postaci *{nazwa}.azure-api.net.* Jeśli chcesz użyć niestandardowej nazwy domeny, zobacz [Konfigurowanie domeny niestandardowej](configure-custom-domain.md). <br/> Nazwa usługi jest używana do odwoływania się do usługi i odpowiedniego zasobu platformy Azure. |
| **Subskrypcja**        | Twoja subskrypcja                             | Subskrypcja, w ramach której zostanie utworzone to nowe wystąpienie usługi. Możesz wybrać jedną z różnych subskrypcji Azure, do których masz dostęp.                                                                                                                                                            |
| **Grupa zasobów**      | *apimResourceGroup*                           | Możesz wybrać nowy lub istniejący zasób. Grupa zasobów jest kolekcją zasobów, które mają ten sam cykl życia, uprawnienia i zasady. Więcej informacji można znaleźć [tutaj](../azure-resource-manager/resource-group-overview.md#resource-groups).                                                                                                  |
| **Lokalizacja**            | *Zachodnie stany USA*                                    | Wybierz region geograficzny w pobliżu. Na liście rozwijanej są wyświetlane tylko regiony dostępne w usłudze API Management.                                                                                                                                                                                                          |
| **Nazwa organizacji**   | Nazwa organizacji                 | Ta nazwa jest używana w wielu miejscach, w tym w tytule portalu dla deweloperów i nadawcy wiadomości e-mail z powiadomieniem.                                                                                                                                                                                                             |
| **Adres e-mail administratora** | *admin\@org.com*                               | Ustaw adres e-mail, na który będą wysyłane wszystkie powiadomienia z usługi **API Management**.                                                                                                                                                                                                                                              |
| **Warstwa cenowa**        | *Developer*                                   | Skonfiguruj warstwę **Developer**, aby ocenić usługę. Ta warstwa nie jest do użytku produkcyjnego. Aby uzyskać więcej informacji na temat skalowania warstw usługi API Management, zobacz [Upgrade and scale](upgrade-and-scale.md) (Uaktualnianie i skalowanie).                                                                                                                                    |

3. Wybierz pozycję **Utwórz**.

    > [!TIP]
    > Zazwyczaj utworzenie usługi API Management trwa 20–30 minut. Wybranie pozycji **Przypnij do pulpitu nawigacyjnego** ułatwia znajdowanie nowo utworzonej usługi.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć, wykonując następujące kroki:

1. W witrynie Azure Portal wybierz pozycję **Wszystkie usługi**.
2. Wpisz wartość `resource groups` w polu wyszukiwania i kliknij wynik.

    ![Nawigacja po grupach zasobów](./media/get-started-create-service-instance/00-DeleteResource-01.png)

3. Znajdź swoją grupę zasobów i kliknij ją.
4. Kliknij pozycję **Usuń grupę zasobów**.

    ![Nawigacja po grupach zasobów](./media/get-started-create-service-instance/00-DeleteResource-02.png)

5. Potwierdź usunięcie, wpisując nazwę grupy zasobów.
6. Kliknij polecenie **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md)
