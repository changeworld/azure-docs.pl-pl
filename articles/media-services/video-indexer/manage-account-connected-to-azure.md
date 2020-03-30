---
title: Zarządzanie kontem indeksatora wideo
titleSuffix: Azure Media Services
description: Dowiedz się, jak zarządzać kontem indeksatora wideo połączonym z platformą Azure.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2019
ms.author: juliako
ms.openlocfilehash: 5b9ec1c04dd90af9d9380a1f4d30386c8f9cc3ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499669"
---
# <a name="manage-a-video-indexer-account-connected-to-azure"></a>Zarządzanie kontem indeksatora wideo połączonym z platformą Azure

W tym artykule pokazano, jak zarządzać kontem indeksatora wideo, które jest połączone z subskrypcją platformy Azure i kontem usługi Azure Media Services.

> [!NOTE]
> Musisz być właścicielem konta indeksatora wideo, aby wykonać zmiany konfiguracji konta omówione w tym temacie.

## <a name="prerequisites"></a>Wymagania wstępne

Połącz swoje konto indeksatora wideo z platformą Azure, zgodnie z opisem w [obszarze Połączone z platformą Azure](connect-to-azure.md).

Pamiętaj, aby postępować zgodnie [z wymaganiami wstępnymi](connect-to-azure.md#prerequisites) i [przejrzeć zagadnienia](connect-to-azure.md#considerations) w artykule.

## <a name="examine-account-settings"></a>Sprawdzanie ustawień konta

W tej sekcji zostaną wyświetlona zawartość ustawień konta indeksatora wideo.

Aby wyświetlić ustawienia:

1. Kliknij ikonę użytkownika w prawym górnym rogu i wybierz pozycję **Ustawienia**.

    ![Ustawienia w indeksatorze wideo](./media/manage-account-connected-to-azure/select-settings.png)

2. Na stronie **Ustawienia** wybierz kartę **Konto.**

Jeśli twoje konto indeksatora wideo jest połączone z platformą Azure, zobaczysz następujące czynności:

* Nazwa podstawowego konta usługi Azure Media Services.
* Liczba zadań indeksowania uruchomionych i w kolejce.
* Liczba i typ przydzielonych jednostek zarezerwowanych.

Jeśli twoje konto wymaga pewnych korekt, na stronie **Ustawienia** zobaczysz odpowiednie błędy i ostrzeżenia dotyczące konfiguracji konta. Wiadomości zawierają łącza do dokładnych miejsc w witrynie Azure portal, w których należy wprowadzić zmiany. Aby uzyskać więcej informacji, zobacz następującej sekcji [błędów i ostrzeżeń.](#errors-and-warnings)

## <a name="repair-the-connection-to-azure"></a>Naprawianie połączenia z platformą Azure

W oknie dialogowym **Aktualizuj połączenie z usługą Azure Media Services** na stronie [indeksatora wideo](https://www.videoindexer.ai/) zostanie wyświetlone monit o podanie wartości dla następujących ustawień:

|Ustawienie|Opis|
|---|---|
|Identyfikator subskrypcji platformy Azure|Identyfikator subskrypcji można pobrać z witryny Azure portal. Kliknij wszystkie **usługi** w lewym panelu i wyszukaj "subskrypcje". Wybierz **subskrypcje** i wybierz żądany identyfikator z listy subskrypcji.|
|Nazwa grupy zasobów usługi Azure Media Services|Nazwa grupy zasobów, w której utworzono konto usługi Media Services.|
|Identyfikator aplikacji|Identyfikator aplikacji usługi Azure AD (z uprawnieniami dla określonego konta usługi Media Services), który został utworzony dla tego konta indeksatora wideo. <br/><br/>Aby uzyskać identyfikator aplikacji, przejdź do witryny Azure portal. W obszarze Konto Usługi Media Services wybierz swoje konto i przejdź do **programu API Access**. Wybierz **pozycję Połącz z interfejsem API usługi Media Services z główną aplikacją** -> **usługi Azure AD**. Skopiuj odpowiednie parametry.|
|Klucz aplikacji|Klucz aplikacji usługi Azure AD skojarzony z kontem usługi Media Services, który został określony powyżej. <br/><br/>Aby uzyskać klucz aplikacji, przejdź do witryny Azure portal. W obszarze Konto Usługi Media Services wybierz swoje konto i przejdź do **programu API Access**. Wybierz **pozycję Połącz z interfejsem API usługi Media Services z jednostką** -> usługi**Zarządzaj** -> certyfikatami aplikacji **& wpisami tajnymi**. Skopiuj odpowiednie parametry.|

## <a name="autoscale-reserved-units"></a>Automatyczne skalowanie jednostek zarezerwowanych

Strona **Ustawienia** umożliwia ustawienie automatycznego skalowania jednostek zarezerwowanych multimediów (RU). Jeśli opcja jest **Wł.,** można przydzielić maksymalną liczbę uruchomień i mieć pewność, że indeksator wideo automatycznie zatrzymuje/uruchamia procesory RUs. Dzięki tej opcji nie płacisz dodatkowych pieniędzy za czas bezczynności, ale także nie czekaj na ukończenie zadań indeksowania przez długi czas, gdy obciążenie indeksowania jest wysokie.

Skalowanie automatyczne nie jest skalowane poniżej 1 RU lub powyżej domyślnego limitu konta usługi Media Services. Aby zwiększyć limit, należy utworzyć żądanie usługi. Aby uzyskać informacje o przydziałach i ograniczeniach oraz o tym, jak otworzyć bilet pomocy technicznej, zobacz [Przydziały i ograniczenia](../../media-services/previous/media-services-quotas-and-limitations.md).

![Indeksator wideo jednostek zarezerwowanych w automatycznym skalowaniu](./media/manage-account-connected-to-azure/autoscale-reserved-units.png)

## <a name="errors-and-warnings"></a>Błędy i ostrzeżenia

Jeśli twoje konto wymaga pewnych korekt, na stronie **Ustawienia** są widoczne odpowiednie błędy i ostrzeżenia dotyczące konfiguracji konta. Wiadomości zawierają łącza do dokładnych miejsc w witrynie Azure portal, w których należy wprowadzić zmiany. W tej sekcji podano więcej szczegółów dotyczących komunikatów o błędach i ostrzeżeniach.

* EventGrid ( EventGrid )

    Musisz zarejestrować dostawcę zasobów EventGrid przy użyciu witryny Azure portal. W [witrynie Azure portal](https://portal.azure.com/)przejdź do **sekcji Subskrypcje** > [subskrypcja] > **ResourceProviders** > **Microsoft.EventGrid**. Jeśli nie jest w stanie **zarejestrowanym,** **wybierz**zarejestruj . Rejestracja zajmuje kilka minut.

* Punkt końcowy przesyłania strumieniowego

    Upewnij się, że podstawowe konto usługi Media Services ma domyślny **punkt końcowy przesyłania strumieniowego** w stanie uruchomionym. W przeciwnym razie nie można oglądać filmów z tego konta usługi Media Services ani z indeksatora wideo.

* Jednostki zarezerwowane dla nośników

    Aby indeksować filmy wideo, należy przydzielić jednostki zarezerwowane multimediów w zasobie usługi Media Service. Aby uzyskać optymalną wydajność indeksowania, zaleca się przydzielić co najmniej 10 jednostek zarezerwowanych S3. Aby uzyskać informacje o cenach, zobacz sekcję często zadawane pytania na stronie [cennik usługi Media Services.](https://azure.microsoft.com/pricing/details/media-services/)

## <a name="next-steps"></a>Następne kroki

Można programowo wchodzić w interakcje z kontem próbnym lub wideoindymatorem, które są połączone z platformą Azure, postępując zgodnie z instrukcjami w: [Użyj interfejsów API](video-indexer-use-apis.md).

Użyj tego samego użytkownika usługi Azure AD, którego użyłeś podczas łączenia się z platformą Azure.
