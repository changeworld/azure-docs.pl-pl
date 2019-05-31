---
title: Jak zarządzać ustawieniami? — Niestandardowy w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Jak zarządzać ustawieniami, Utwórz obszar roboczy, Udostępnij obszar roboczy i samodzielnie zarządzać kluczem subskrypcji w niestandardowych w usłudze Translator.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-pawal
ms.topic: conceptual
ms.openlocfilehash: fb766a8cc6ae0de2407f44982a58fcc448e4b00c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66382669"
---
# <a name="how-to-manage-settings"></a>Jak zarządzać ustawieniami

Na stronie Ustawienia niestandardowe w usłudze Translator mogą Utwórz nowy obszar roboczy, Udostępnij obszar roboczy i dodaje lub modyfikuje klucz subskrypcji Translation firmy Microsoft.

Aby przejść do strony ustawień:

1. Zaloguj się do [niestandardowe w usłudze Translator](https://portal.customtranslator.azure.ai/) portalu.
2. W portalu niestandardowe w usłudze Translator kliknij ikonę koła zębatego na pasku bocznym.

    ![Ustawienie Linku](media/how-to/how-to-settings.png)

## <a name="associating-microsoft-translator-subscription"></a>Kojarzenie subskrypcji usługi Microsoft Translator

Musisz mieć interfejs API tekstu usługi Translator firmy Microsoft klucz subskrypcji skojarzonych z obszaru roboczego do uczenia lub wdrażania modeli.

Jeśli nie masz subskrypcji, wykonaj następujące czynności:

1. Subskrybuj interfejs API tekstu usługi Microsoft Translator. W tym artykule przedstawiono sposób subskrybowania dla interfejsu API tekstu usługi Translator firmy Microsoft.
2. Zanotuj klucz dla Twojej subskrypcji w usłudze translator. Klucz1 lub klucz2 są akceptowane.
3. Przejdź z powrotem do portalu niestandardowe w usłudze Translator.

### <a name="add-existing-key"></a>Dodawanie istniejącego klucza

1.  Przejdź do strony "Ustawienia" dla obszaru roboczego.
2.  Kliknij przycisk Dodaj klucz

    ![Jak dodać klucz subskrypcji](media/how-to/how-to-add-subscription-key.png)

3. W oknie dialogowym Wprowadź klucz dla Twojej subskrypcji w usłudze translator, a następnie kliknij przycisk "Dodaj".

    ![Jak dodać klucz subskrypcji](media/how-to/how-to-add-subscription-key-dialog.png)
4.  Po dodaniu klucza można zmodyfikować lub usunąć klucz w dowolnym momencie.

    ![Dodaj klucz subskrypcji](media/how-to/subscription-key-after-add.png)

## <a name="manage-your-workspace"></a>Zarządzanie obszarem roboczym

Obszar roboczy jest obszar roboczy, do redagowania i tworzenia systemu tłumaczenia niestandardowych. Obszar roboczy może zawierać wiele projektów, modeli i dokumentów.

Jeśli różne część swojej pracy musi być udostępniane osobom w innej, może być przydatne utworzenie wielu obszarów roboczych.

## <a name="create-a-new-workspace"></a>Utwórz nowy obszar roboczy

1.  Przejdź do strony "Ustawienia" obszaru roboczego.
2.  Kliknij pozycję "nowy obszar roboczy" przycisk w sekcji "Utwórz nowy obszar roboczy".

    ![Utwórz nowy obszar roboczy](media/how-to/create-new-workspace.png)

4.  W oknie dialogowym Wprowadź nazwę nowego obszaru roboczego.
5.  Kliknij przycisk "Utwórz".

    ![Utwórz nowy obszar roboczy okna dialogowego](media/how-to/create-new-workspace-dialog.png)

## <a name="share-your-workspace"></a>Udostępnij obszar roboczy

W niestandardowych w usłudze Translator można udostępniać obszaru roboczego z innymi osobami, jeśli różne część swojej pracy musi być udostępniane osobom w innej.

1.  Przejdź do strony "Ustawienia" obszaru roboczego.
2.  Kliknij przycisk "Udostępnij" w sekcji "Ustawienia udostępniania".

    ![Udostępnij obszar roboczy](media/how-to/share-workspace.png)

3.  W oknie dialogowym wprowadź rozdzielaną przecinkami listę adresów e-mail, które chcesz, aby ten obszar roboczy udostępnione. Upewnij się, udostępnić za pomocą adresu e-mail tej osoby używa się do niestandardowego w usłudze Translator z. Następnie wybierz odpowiedni poziom uprawnień do udostępniania.

4.  Jeśli obszar roboczy nadal ma domyślną nazwę "Mój obszar roboczy", trzeba będzie je zmienić przed udostępnieniem obszaru roboczego.
5.  Kliknij przycisk "Zapisz".

## <a name="sharing-permissions"></a>Uprawnienia do udostępniania

1.  **Czytnik:** Czytnik w obszarze roboczym będą mogli wyświetlać wszystkie informacje w obszarze roboczym.

2.  **Edytor:** Edytor w obszarze roboczym będą mogli dodać dokumenty, uczyć modele i usuwać dokumenty i projektów. One można dodać klucz subskrypcji, ale nie można modyfikować, który jest udostępniany przez obszar roboczy, usunąć obszar roboczy lub zmienić nazwę obszaru roboczego.

3.  **Właściciel:** Właściciel ma pełne uprawnienia do obszaru roboczego.

## <a name="change-sharing-permission"></a>Zmiana uprawnień do udostępniania

Jeśli obszar roboczy jest udostępniony, w sekcji "Ustawienia udostępniania" pokazuje wszystkich adresów e-mail, które są udostępniane przez ten obszar roboczy. Możesz zmienić istniejące udostępniania uprawnień dla każdego z adresów e-mail, jeśli masz dostęp właściciela do obszaru roboczego.

1.  W sekcji "Ustawienia udostępniania" dla każdej wiadomości e-mail menu rozwijane zawiera bieżący poziom uprawnień.

2.  Kliknij menu rozwijane i wybierz nowy poziom uprawnień, które chcesz przypisać do tego adresu e-mail.

    ![Udostępnianie ustawień uprawnień](media/how-to/sharing-permission-settings.png)

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, [jak przeprowadzić migrację do swojego obszaru roboczego i projektu](how-to-migrate.md) z [Centrum w usłudze Translator firmy Microsoft](https://hub.microsofttranslator.com)
