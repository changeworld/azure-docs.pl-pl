---
title: Migrowanie baz wiedzy (wersja zapoznawcza) — Qna Maker
titleSuffix: Azure Cognitive Services
description: Baz wiedzy utworzonych za pomocą bezpłatnej wersji zapoznawczej usługi QnA Maker będą musieli przeprowadzić migrację do usługi QnA Maker GA. Usługa QnA Maker w wersji zapoznawczej zostaną wycofane w styczniu 2019 r.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 261d42a6c21681ee8015edfe73ad78b4458eb0ee
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078818"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrowanie bazy wiedzy przy użyciu eksportu / importu
Narzędzie QnA Maker ogłosiliśmy ogólną dostępność 7 maja 2018 r. w \\\build\ konferencji. Usługa QnA Maker GA ma Nowa architektura oparta na platformie Azure. Baz wiedzy utworzonych za pomocą bezpłatnej wersji zapoznawczej usługi QnA Maker będą musieli przeprowadzić migrację do usługi QnA Maker GA. Usługa QnA Maker w wersji zapoznawczej zostaną wycofane w styczniu 2019 r. Aby uzyskać więcej informacji na temat zmian w wersji ogólnie dostępnej usługi QnA Maker, zobacz powiadomienie usługi QnA Maker GA [wpis w blogu](https://aka.ms/qnamakerga-blog).

Usługa QnA Maker ma teraz [modelu cen](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/).

Wymagania wstępne
> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * Konfigurowanie nowego [usługę QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Migrowanie bazy wiedzy z portalu usługi QnA Maker w wersji zapoznawczej
1. Przejdź do [portalu usługi QnA Maker w wersji zapoznawczej](https://aka.ms/qnamaker-old-portal
) i kliknij pozycję **wykaz usług**.
2. Wybierz wiedzy, które mają zostać zmigrowane, klikając ikonę edycji.

    ![Edytowanie wiedzy](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Kliknij pozycję **Pobierz wiedzy** Aby pobrać plik tsv z zawartością bazy wiedzy — pytań, odpowiedzi, metadane, oraz nazwy źródła danych, z którym zostały wyodrębnione.

    ![Pobierz bazy wiedzy](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Zaloguj się do [portalu narzędzia QnA Maker](https://qnamaker.ai) przy użyciu poświadczeń platformy azure i kliknij pozycję **tworzenie bazy wiedzy**.
    
5. Jeśli nie utworzono już usługę QnA Maker, wybierz opcję **tworzenie pytań i odpowiedzi usługi**. W przeciwnym razie wybierz usługę QnA Maker z listy rozwijane w kroku 2. Wybierz usługę QnA Maker, który będzie hostem bazy wiedzy Knowledge Base.

    ![Usługa QnA Instalatora](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Utwórz pusty bazy wiedzy 

    ![Zestaw źródeł danych](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Nadaj usługi **nazwy.** Zduplikowane nazwy są obsługiwane i znaki specjalne są również obsługiwane.
    - Pomiń przekazywanie plików lub adresów URL, w których chcesz użyć danych z bazy wiedzy (wersja zapoznawcza). Teraz utworzysz pustą bazy wiedzy knowledge base.

7. Wybierz pozycję **Utwórz**.

    ![Tworzenie bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb.png)

8. W tej nowej bazie wiedzy, otwórz **ustawienia** kartę, a następnie kliknij polecenie **importu wiedzy**. Importuje pytań, odpowiedzi i metadanych i przechowuje nazwy źródeł danych, z których zostały wyodrębnione.

   ![Importowanie bazy wiedzy](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Test** bazie wiedzy za pomocą panelu testu. Dowiedz się, jak [test bazy wiedzy](../How-To/test-knowledge-base.md).
10. **Publikowanie** bazy wiedzy knowledge base. Dowiedz się, jak [Opublikuj bazę wiedzy](../How-To/publish-knowledge-base.md).
11. W kodzie aplikacji lub dowolnemu botowi, należy używać punktu końcowego poniżej. Zobacz tutaj jak [tworzenia bota pytań i odpowiedzi](../Tutorials/create-qna-bot.md).

    ![Usługa QnA Maker wartości](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

W tym momencie całą wiedzy zawartość — pytania i odpowiedzi metadanych, wraz z nazwy plików źródłowych oraz w adresach URL, zostaną zaimportowane do nowej bazy wiedzy. 

## <a name="chatlogs-and-alterations"></a>Chatlogs i zmiany
Zmiany (synonimy) nie są importowane automatycznie. Użyj [interfejsów API w wersji 2](https://aka.ms/qnamaker-v2-apis) do wyeksportowania zmiany ze stosu (wersja zapoznawcza) i [interfejsów API w wersji 4](https://aka.ms/qnamaker-v4-apis) do zastąpienia w nowy stos.

Nie ma możliwości migracji chatlogs, ponieważ nowy stos przy użyciu usługi Application Insights chatlogs. Jednak można pobrać chatlogs z [portalu w wersji zapoznawczej](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Edytowanie wiedzy](../How-To/edit-knowledge-base.md)
