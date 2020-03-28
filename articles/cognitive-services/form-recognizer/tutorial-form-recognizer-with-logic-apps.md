---
title: 'Samouczek: Używanie aparatu rozpoznawania formularzy z aplikacjami logiki azure do analizowania faktur — aparat rozpoznawania formularzy'
titleSuffix: Azure Cognitive Services
description: W tym samouczku użyjesz rozpoznawania formularzy za pomocą usługi Azure Logic Apps, aby utworzyć przepływ pracy, który automatyzuje proces szkolenia modelu i testowania go przy użyciu przykładowych danych.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118270"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Samouczek: Analizowanie faktur za pomocą aparatu rozpoznawania formularzy w aplikacjach logiki azure

W tym samouczku utworzysz przepływ pracy w usłudze Azure Logic Apps, który używa rozpoznawania formularzy, usługi, która jest częścią pakietu Azure Cognitive Services, do wyodrębniania danych z faktur. Najpierw trenujesz model rozpoznawania formularzy przy użyciu przykładowego zestawu danych, a następnie testujesz model na innym zestawie danych.

Oto, co obejmuje ten poradnik:

> [!div class="checklist"]
> * Poproś o dostęp do aparatu rozpoznawania formularzy
> * Tworzenie kontenera obiektów blob usługi Azure Storage
> * Przekazywanie przykładowych danych do kontenera obiektów blob platformy Azure
> * Tworzenie aplikacji logiki platformy Azure
> * Konfigurowanie aplikacji logiki do używania zasobu aparatu rozpoznawania formularzy
> * Testowanie przepływu pracy przez uruchomienie aplikacji logiki

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz bezpłatną subskrypcję.](https://azure.microsoft.com/free/)

## <a name="understand-the-invoice-to-be-analyzed"></a>Zrozumienie faktury do analizy

Przykładowy zestaw danych, który będzie używany do szkolenia i testowania modelu, jest dostępny jako plik zip z [gitHub.](https://go.microsoft.com/fwlink/?linkid=2090451) Pobierz i wyodrębnij plik zip i otwórz plik PDF faktury w folderze **/Train.** Należy zauważyć, że ma tabelę z numerem faktury, datą faktury i tak dalej. 

> [!div class="mx-imgBorder"]
> ![Przykładowa faktura](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

W tym samouczku dowiesz się, jak użyć przepływu pracy usługi Azure Logic Apps, aby wyodrębnić informacje z tabel takich jak te do formatu JSON.

## <a name="create-an-azure-storage-blob-container"></a>Tworzenie kontenera obiektów blob usługi Azure Storage

Ten kontener służy do przekazywania przykładowych danych, które są wymagane do szkolenia modelu.

1. Postępuj zgodnie z instrukcjami w [tworzenie konta usługi Azure Storage,](../../storage/common/storage-account-create.md) aby utworzyć konto magazynu. Użyj **funkcji formrecostorage** jako nazwy konta magazynu.
1. Postępuj zgodnie z instrukcjami w [Tworzenie kontenera obiektów blob platformy Azure,](../../storage/blobs/storage-quickstart-blobs-portal.md) aby utworzyć kontener na koncie usługi Azure Storage. Użyj **formrecocontainer** jako nazwa kontenera. Upewnij się, że poziom dostępu publicznego został ustawiony na **Container (anonimowy dostęp do odczytu kontenerów i obiektów blob)**.

    > [!div class="mx-imgBorder"]
    > ![Tworzenie kontenera obiektów blob](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Przekazywanie przykładowych danych do kontenera obiektów blob platformy Azure

Pobierz przykładowe dane dostępne w [usłudze GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Wyodrębnij dane do folderu lokalnego i przekaż zawartość folderu **/Train** do utworzonego wcześniej **folderu formrecocontainer.** Postępuj zgodnie z instrukcjami w [przekaż blokowy obiekt blob,](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) aby przekazać dane do kontenera.

Skopiuj adres URL kontenera. Ten adres URL będzie potrzebny w dalszej części samouczka. Jeśli utworzono konto magazynu i kontener o tych samych nazwach, które zostały wymienione w tym samouczku, adres URL będzie *https:\//formrecostorage.blob.core.windows.net/formrecocontainer/*.

## <a name="create-a-form-recognizer-resource"></a>Tworzenie zasobu aparatu rozpoznawania formularzy

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

Usługi Azure Logic Apps umożliwiają automatyzację i organizowanie zadań i przepływów pracy. W tym samouczku utworzysz aplikację logiki, która jest wyzwalana przez otrzymanie faktury, którą chcesz analizować jako załącznik wiadomości e-mail. W tym przepływie pracy należy wykonać następujące zadania:
* Skonfiguruj aplikację logiki, aby wyzwalała się automatycznie po otrzymaniu wiadomości e-mail z dołączoną fakturą.
* Skonfiguruj aplikację logiki, aby używać operacji **modelu pociągu** aparatu rozpoznawania formularzy do szkolenia modelu przy użyciu przykładowych danych przekazanych do magazynu obiektów blob platformy Azure.
* Skonfiguruj aplikację logiki, aby używać operacji **analizy formularza** rozpoznawania formularzy do używania już uczonego modelu. Ten składnik przeanalizuje fakturę, która zostanie udostępniona do tej aplikacji logiki na podstawie modelu, który trenował wcześniej.

Wykonaj następujące kroki, aby skonfigurować przepływ pracy.

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz aplikację** > **logiki****integracji** > zasobów .

1. W obszarze **Tworzenie aplikacji logiki** podaj szczegółowe informacje o aplikacji logiki, jak pokazano poniżej. Po zakończeniu wybierz pozycję **Utwórz**.

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*nazwa aplikacji logiki*> | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry, łączniki`-`( ), podkreślenia (`_`), nawiasy (`(`, `)`), i kropki (`.`). W tym przykładzie użyto "My-First-Logic-App". |
   | **Subskrypcja** | <*Nazwa subskrypcji platformy Azure*> | Twoja nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | <*Nazwa-grupa zasobów platformy Azure*> | Nazwa grupy [zasobów platformy Azure](./../../azure-resource-manager/management/overview.md) używana do organizowania powiązanych zasobów. W tym przykładzie użyto "My-First-LA-RG". |
   | **Lokalizacja** | <*Region platformy Azure*> | Region, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie użyto "Zachodnie stany USA". |
   | **Analiza dzienników** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **Powiadomienia** > **Przejdź do zasobu** dla wdrożonej aplikacji logiki. Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   > [!div class="mx-imgBorder"]
   > ![Wybieranie pustego szablonu dla aplikacji logiki](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Konfigurowanie aplikacji logiki w celu wyzwolenia przepływu pracy po odebraniu wiadomości e-mail

W tym samouczku wyzwolić przepływ pracy po odebraniu wiadomości e-mail z załączoną fakturą. Ten samouczek używa usługi Office 365 jako usługi poczty e-mail, ale można użyć dowolnego innego dostawcy poczty e-mail, którego chcesz użyć.

1. Na kartach wybierz pozycję Wszystkie, wybierz pozycję **Office 365 Outlook**, a następnie w obszarze **Wyzwalacze**wybierz pozycję **Po nadejściu nowej wiadomości e-mail**.

    ![Wyzwalanie aplikacji logiki za pośrednictwem przychodzącej wiadomości e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. W polu **Programu Outlook usługi Office 365** kliknij pozycję Zaloguj **się**i wprowadź szczegóły, aby zalogować się do konta usługi Office 365.

1. W następnym oknie dialogowym wykonaj następujące czynności.
    1. Wybierz folder, który powinien być monitorowany pod kątem każdej nowej wiadomości e-mail.
    1. W **yjmując załączniki**, wybierz pozycję **Tak**. Gwarantuje to, że tylko wiadomości e-mail z załącznikami wyzwalają przepływ pracy.
    1. W **ybranych załączników**wybierz pozycję **Tak**. Gwarantuje to, że zawartość załącznika są używane w dalszym przetwarzaniu.

        > [!div class="mx-imgBorder"]
        > ![Konfigurowanie wyzwalacza wiadomości e-mail aplikacji logiki](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Kliknij **pozycję Zapisz** na pasku narzędzi u góry.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Konfigurowanie aplikacji logiki do używania operacji modelu pociągu aparatu rozpoznawania formularzy

Aby można było użyć usługi rozpoznawania formularzy do analizowania faktur, należy przeszkolić model, udostępniając mu kilka przykładowych danych faktur, które model może analizować i uczyć się od.

1. Wybierz **pozycję Nowy krok**i w obszarze Wybierz **akcję**wyszukaj pozycję Aparat **rozpoznawania formularzy**. Na podstawie wyników, które są wyświetlane w górę, wybierz pozycję **Aparat rozpoznawania formularzy**, a następnie w obszarze akcje, które są dostępne dla aparatu rozpoznawania formularzy, wybierz opcję **Model pociągu**.

    > [!div class="mx-imgBorder"]
    > ![Trenuj model aparatu rozpoznawania formularzy](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. W oknie dialogowym Rozpoznawanie formularzy podaj nazwę połączenia i wprowadź adres URL punktu końcowego i klucz pobrany dla zasobu aparat rozpoznawania formularzy.

    > [!div class="mx-imgBorder"]
    > ![Nazwa połączenia dla aparatu rozpoznawania formularzy](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Kliknij przycisk **Utwórz**.

1. W oknie dialogowym **Model pociągu** dla **źródła**wprowadź adres URL kontenera, w którym zostały przekazane przykładowe dane.

    > [!div class="mx-imgBorder"]
    > ![Kontener do przechowywania przykładowych faktur](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Kliknij **pozycję Zapisz** na pasku narzędzi u góry.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Konfigurowanie aplikacji logiki do używania operacji analizowania formularza aparat rozpoznawania formularzy

W tej sekcji należy dodać operację **Analizuj formularz** do przepływu pracy. Ta operacja używa już przeszkolony model do analizy nowej faktury, która jest dostarczana do aplikacji logiki.

1. Wybierz **pozycję Nowy krok**i w obszarze Wybierz **akcję**wyszukaj pozycję Aparat **rozpoznawania formularzy**. Na podstawie wyników, które się pojawią, wybierz pozycję **Aparat rozpoznawania formularzy**, a następnie w obszarze akcje dostępne dla aparatu rozpoznawania formularzy wybierz pozycję **Analizuj formularz**.

    > [!div class="mx-imgBorder"]
    > ![Analizowanie modelu rozpoznawania formularzy](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. W oknie **dialogowym Analizowanie formularza** wykonaj następujące czynności:

    1. Kliknij pole **tekstowe Identyfikator modelu,** a następnie w oknie dialogowym, które się otworzy, w obszarze **Dynamiczna zawartość** kartę wybierz **modelId**. W ten sposób należy podać aplikację przepływu z identyfikatorem modelu modelu, który został przeszkolony w ostatniej sekcji.

        > [!div class="mx-imgBorder"]
        > ![Użyj identyfikatora modelu dla aparatu rozpoznawania formularzy](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Kliknij pole **tekstowe Dokumentu,** a następnie w oknie dialogowym, które się otworzy, na karcie **Zawartość dynamiczna** wybierz pozycję **Zawartość załączników**. Spowoduje to skonfigurowanie przepływu do używania przykładowego pliku faktury dołączonego do wiadomości e-mail wyzwalającego przepływ pracy.

        > [!div class="mx-imgBorder"]
        > ![Analizowanie faktur za pomocą załącznika wiadomości e-mail](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Kliknij **pozycję Zapisz** na pasku narzędzi u góry.

### <a name="extract-the-table-information-from-the-invoice"></a>Wyodrębnianie informacji o tabeli z faktury

W tej sekcji można skonfigurować aplikację logiki, aby wyodrębnić informacje z tabeli w fakturach.

1. Wybierz **pozycję Dodaj akcję**, a następnie w obszarze Wybierz **akcję**wyszukaj **pozycję Redagowanie** i w obszarze dostępne akcje wybierz pozycję **Redagowanie** ponownie.
    ![Wyodrębnianie informacji o tabeli z faktury](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. W oknie dialogowym **Redagowanie** kliknij pole tekstowe **Wejścia,** a następnie w oknie dialogowym wybierz **tabele**.

    > [!div class="mx-imgBorder"]
    > ![Wyodrębnianie informacji o tabeli z faktury](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Kliknij przycisk **Zapisz**.

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby przetestować aplikację logiki, użyj przykładowych faktur w folderze **/Test** przykładowego zestawu danych pobranego z [gitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Wykonaj następujące kroki:

1. W projektancie usługi Azure Logic Apps dla aplikacji wybierz pozycję **Uruchom** z paska narzędzi u góry. Przepływ pracy jest teraz aktywny i czeka na otrzymanie wiadomości e-mail z dołączoną fakturą.
1. Wyślij wiadomość e-mail z przykładową fakturą dołączoną do adresu e-mail podanego podczas tworzenia aplikacji logiki. Upewnij się, że wiadomość e-mail jest dostarczana do folderu, który został podany podczas konfigurowania aplikacji logiki.
1. Jak tylko wiadomość e-mail zostanie dostarczona do folderu, Projektant aplikacji logiki pokazuje ekran z postępem każdego etapu. Na poniższym zrzucie ekranu widać, że wiadomość e-mail z załącznikiem jest odbierana i przepływ pracy jest w toku.

    > [!div class="mx-imgBorder"]
    > ![Uruchamianie przepływu pracy przez wysłanie wiadomości e-mail](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. Po zakończeniu wszystkich etapów przepływu pracy projektant aplikacji logiki pokazuje zielone pole wyboru względem każdego etapu. W oknie projektanta wybierz pozycję **Dla każdego 2**, a następnie wybierz pozycję **Redagowanie**.

    > [!div class="mx-imgBorder"]
    > ![Ukończono przepływ pracy](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Z pola **OUTPUTS** skopiuj dane wyjściowe i wklej go do dowolnego edytora tekstu.

1. Porównaj dane wyjściowe JSON z przykładową fakturą wysłaną jako załącznik w wiadomości e-mail. Sprawdź, czy dane JSON odpowiadają danym w tabeli w fakturze.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    Pomyślnie ukończono ten samouczek!

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurować przepływ pracy usługi Azure Logic Apps, aby użyć rozpoznawania formularzy do szkolenia modelu i wyodrębniania zawartości faktury. Następnie dowiedz się, jak utworzyć zestaw danych szkoleniowych, dzięki czemu można utworzyć podobny scenariusz z własnymi formularzami.

> [!div class="nextstepaction"]
> [Tworzenie zestawu danych szkoleniowych](build-training-data-set.md)