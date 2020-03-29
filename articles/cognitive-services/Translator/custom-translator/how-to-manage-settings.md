---
title: Jak zarządzać ustawieniami? - Tłumacz niestandardowy
titleSuffix: Azure Cognitive Services
description: Jak zarządzać ustawieniami, tworzyć obszar roboczy, udostępniać obszar roboczy i zarządzać kluczem subskrypcji w usłudze Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f90ada6b6cd1760b3b779010625f252533fa611d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219512"
---
# <a name="how-to-manage-settings"></a>Jak zarządzać ustawieniami

Na stronie Ustawienia usługi Tłumacz niestandardowy można utworzyć nowy obszar roboczy, udostępnić obszar roboczy oraz dodać lub zmodyfikować klucz subskrypcji tłumaczenia firmy Microsoft.

Aby uzyskać dostęp do strony ustawień:

1. Zaloguj się do portalu [translatora niestandardowego.](https://portal.customtranslator.azure.ai/)
2. Na portalu Custom Translator kliknij ikonę koła zębatego na pasku bocznym.

    ![Łącze ustawień](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Kojarzenie subskrypcji usługi Microsoft Translator

Aby szkolić lub wdrażać modele, należy mieć klucz subskrypcji interfejsu API programu Microsoft Translator text skojarzony z obszarem roboczym.

Jeśli nie masz subskrypcji, wykonaj poniższe czynności:

1. Subskrybuj interfejs API tekstu usługi Microsoft Translator. W tym artykule pokazano, jak subskrybować interfejs API tekstu usługi Microsoft Translator.
2. Zanotuj klucz subskrypcji tłumacza. Jeden z Key1 lub Key2 są dopuszczalne.
3. Przejdź z powrotem do portalu usługi Translator niestandardowy.

### <a name="add-existing-key"></a>Dodawanie istniejącego klucza

1.  Przejdź do strony "Ustawienia" obszaru roboczego.
2.  Kliknij pozycję Dodaj klucz

    ![Jak dodać klucz subskrypcji](media/how-to/how-to-add-subscription-key.png)

3. W oknie dialogowym wprowadź klucz subskrypcji tłumacza, a następnie kliknij przycisk "Dodaj".

    ![Jak dodać klucz subskrypcji](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Po dodaniu klucza można zmodyfikować lub usunąć klucz w dowolnym momencie.

    ![Klucz subskrypcji po dodaniu](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Zarządzanie obszarem roboczym

Obszar roboczy to obszar roboczy do tworzenia i tworzenia niestandardowego systemu tłumaczenia. Obszar roboczy może zawierać wiele projektów, modeli i dokumentów.

Jeśli inna część pracy musi być udostępniona różnym osobom, może być przydatne utworzenie wielu obszarów roboczych.

## <a name="create-a-new-workspace"></a>Tworzenie nowego obszaru roboczego

1.  Przejdź do strony "Ustawienia" obszaru roboczego.
2.  Kliknij przycisk "Nowy obszar roboczy" w sekcji "Utwórz nowy obszar roboczy".

    ![Tworzenie nowego obszaru roboczego](media/how-to/create-new-workspace.png)

4.  W oknie dialogowym wprowadź nazwę nowego obszaru roboczego.
5.  Kliknij przycisk "Utwórz".

    ![Tworzenie nowego okna dialogowego obszaru roboczego](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Udostępnianie obszaru roboczego

W usłudze Custom Translator możesz udostępniać swój obszar roboczy innym osobom, jeśli inna część pracy musi być udostępniona innym osobom.

1.  Przejdź do strony "Ustawienia" obszaru roboczego.
2.  Kliknij przycisk "Udostępnij" w sekcji "Ustawienia udostępniania".

    ![Udostępnianie obszaru roboczego](media/how-to/share-workspace.png)

3.  W oknie dialogowym wprowadź listę adresów e-mail oddzielonych przecinkami, którym chcesz udostępnić ten obszar roboczy. Upewnij się, że udostępniasz adres e-mail używany przez tę osobę do logowania się do usługi Custom Translator. Następnie wybierz odpowiedni poziom uprawnień do udostępniania.

4.  Jeśli obszar roboczy nadal ma domyślną nazwę "Mój obszar roboczy", musisz go zmienić przed udostępnieniem obszaru roboczego.
5.  Kliknij przycisk "Zapisz".

## <a name="sharing-permissions"></a>Uprawnienia do udostępniania

1.  **Czytnik:** Czytnik w obszarze roboczym będzie mógł wyświetlić wszystkie informacje w obszarze roboczym.

2.  **Redaktor:** Edytor w obszarze roboczym będzie mógł dodawać dokumenty, szkolić modele oraz usuwać dokumenty i projekty. Mogą dodać klucz subskrypcji, ale nie mogą modyfikować, komu obszar roboczy jest współdzielony, usuwać obszar roboczy ani zmieniać nazwy obszaru roboczego.

3.  **Właściciel:** Właściciel ma pełne uprawnienia do obszaru roboczego.

## <a name="change-sharing-permission"></a>Zmienianie uprawnień udostępniania

Gdy obszar roboczy jest udostępniony, sekcja "Ustawienia udostępniania" pokazuje wszystkie adresy e-mail, którym udostępnia się ten obszar roboczy. Możesz zmienić istniejące uprawnienia udostępniania dla każdego adresu e-mail, jeśli masz dostęp właściciela do obszaru roboczego.

1.  W sekcji "Ustawienia udostępniania" dla każdej wiadomości e-mail menu rozwijane pokazuje aktualny poziom uprawnień.

2.  Kliknij menu rozwijane i wybierz nowy poziom uprawnień, który chcesz przypisać do tego adresu e-mail.

    ![Ustawienia uprawnień udostępniania](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak przeprowadzić migrację obszaru roboczego i projektu](how-to-migrate.md) z centrum Microsoft Translator [Hub](https://hub.microsofttranslator.com)
