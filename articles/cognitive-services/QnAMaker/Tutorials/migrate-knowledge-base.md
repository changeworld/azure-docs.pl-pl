---
title: Sposób importu wiedzy - kognitywnych usług firmy Microsoft | Dokumentacja firmy Microsoft
titleSuffix: Azure
description: Importowanie bazy wiedzy
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce8f98f9bdb37d5f326e942fe5b5e815e5272c56
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355940"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrowanie bazy wiedzy przy użyciu eksportu / importu
Maker — strona główna udostępniła na 7 maja 2018 na \\konferencji \build\. GA Maker — strona główna ma Nowa architektura oparta na platformie Azure. Utworzone za pomocą podglądu wolnego Maker — strona główna bazy wiedzy, należy przeprowadzić migrację do po Maker — strona główna Podgląd Maker — strona główna zostaną wycofane w listopadzie 2018. Aby uzyskać więcej informacji na temat zmian w GA Maker — strona główna, zobacz anons GA Maker — strona główna [wpis w blogu](https://aka.ms/qnamakerga-blog).

Ma teraz Maker — strona główna [model cenowy](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/).

Wymagania wstępne
> [!div class="checklist"]
> * Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * Konfiguracja nowej [usługi Maker — strona główna](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Migrowanie bazy wiedzy z portalu w wersji zapoznawczej Maker — strona główna
1. Przejdź do [portalu w wersji zapoznawczej Maker — strona główna](https://aka.ms/qnamaker-old-portal
) i wybierz polecenie **Moje usług**.
2. Wybierz wiedzy, które chcesz migrować, klikając ikonę edycji.

    ![Edytuj wiedzy](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Polecenie **Pobierz wiedzy** pobrania pliku .tsv z zawartością bazy wiedzy — pytania, odpowiedzi i metadanych, a nazwy źródła danych, z którym zostały wyodrębnione.

    ![Pobierz wiedzy](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Zaloguj się do [Maker — strona główna portalu](https://qnamaker.ai) przy użyciu poświadczeń platformy azure i kliknij przycisk **Utwórz nową usługę**.

    ![Tworzenie bazy wiedzy ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. Jeśli nie utworzono już Maker — strona główna usługi, wybierz **Tworzenie usługi — strona główna**. W przeciwnym razie wybierz usługę Maker — strona główna listach rozwijanych w kroku 2. Wybierz usługę — strona główna Maker, który będzie hostem bazy wiedzy Knowledge Base.

    ![Skonfiguruj usługę — strona główna](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Utwórz pusty bazy wiedzy 

    ![Zestaw źródeł danych](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Nadaj usługi **nazwy.** Zduplikowane nazwy są obsługiwane i znaki specjalne są również obsługiwane.
    - Pomiń przekazywanie plików lub adresów URL, w których chcesz użyć danych z bazy wiedzy w wersji zapoznawczej. Teraz zostanie utworzona pusta baza wiedzy.

7. Wybierz pozycję **Utwórz**.

    ![Tworzenie bazy wiedzy](../media/qnamaker-how-to-create-kb/create-kb.png)

8. W tej nowej wiedzy, otwórz **ustawienia** i kliknij polecenie **importu wiedzy**. Importuje pytania, odpowiedzi i metadanych i przechowuje nazwy źródeł danych, z których zostały wyodrębnione.

   ![Importu wiedzy](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Test** bazie wiedzy za pomocą panelu testu. Dowiedz się, jak [testowanie bazy wiedzy](../How-To/test-knowledge-base.md).
10. **Publikowanie** bazy wiedzy knowledge base. Dowiedz się, jak [publikowania bazy wiedzy](../How-To/publish-knowledge-base.md).
11. W kodzie aplikacji lub bot, użyj punktu końcowego poniżej. Zobacz tutaj jak [utworzyć bot — strona główna](../Tutorials/create-qna-bot.md).

    ![Wartości Maker — strona główna](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

W tym momencie wszystkie wiedzy zawartości — pytania, odpowiedzi i metadanych, wraz z nazwami plików źródłowych i adresy URL, są importowane do nowej wiedzy. 

## <a name="chatlogs-and-alterations"></a>Chatlogs i zmiany
Zmiany (synonimy) nie są automatycznie importowane. Użyj [interfejsów API w wersji 2](https://aka.ms/qnamaker-v2-apis) można wyeksportować zmiany ze stosu Podgląd i [interfejsów API w wersji 4](https://aka.ms/qnamaker-v4-apis) zastąpić nowego stosu.

Nie istnieje sposób migracji chatlogs, ponieważ stos nowej używa usługi Application Insights do przechowywania chatlogs. Jednak można pobrać chatlogs z [portalu w wersji zapoznawczej](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Edytuj bazy wiedzy](../How-To/edit-knowledge-base.md)
