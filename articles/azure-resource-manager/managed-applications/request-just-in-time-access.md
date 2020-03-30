---
title: Żądanie dostępu just in time
description: W tym artykule opisano, jak wydawcy aplikacji zarządzanych platformy Azure żądają dostępu just-in-time do aplikacji zarządzanej.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651386"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Włączanie i żądanie dostępu just-in-time dla aplikacji zarządzanych platformy Azure

Konsumenci aplikacji zarządzanej mogą być niechętni do udzielenia stałego dostępu do zarządzanej grupy zasobów. Jako wydawca aplikacji menedżera możesz preferować, aby konsumenci wiedzieli dokładnie, kiedy trzeba uzyskać dostęp do zarządzanych zasobów. Aby zapewnić konsumentom większą kontrolę nad udzielaniem dostępu do zasobów zarządzanych, usługa Azure Managed Applications udostępnia funkcję o nazwie dostęp just-in-time (JIT), która jest obecnie w wersji zapoznawczej.

Dostęp JIT umożliwia żądanie podwyższonego dostępu do zasobów aplikacji zarządzanej w celu rozwiązywania problemów lub konserwacji. Zawsze masz dostęp tylko do odczytu do zasobów, ale przez określony okres możesz mieć większy dostęp.

Przepływ pracy w celu przyznania dostępu wynosi:

1. Dodaj aplikację zarządzaną do portalu marketplace i określ, że dostęp JIT jest dostępny.

1. Podczas wdrażania konsument umożliwia dostęp JIT dla tego wystąpienia aplikacji zarządzanej.

1. Po wdrożeniu konsument może zmienić ustawienia dostępu JIT.

1. Wyślij żądanie dostępu, gdy trzeba rozwiązać lub zaktualizować zasoby zarządzane.

1. Konsument zatwierdza Twoje żądanie.

W tym artykule skupiono się na działaniach podejmowanych przez wydawców w celu umożliwienia dostępu JIT i przesyłania żądań. Aby dowiedzieć się więcej o zatwierdzaniu żądań dostępu JIT, zobacz [Zatwierdzanie dostępu just-in-time w aplikacjach zarządzanych platformy Azure.](approve-just-in-time-access.md)

## <a name="add-jit-access-step-to-ui"></a>Dodawanie kroku dostępu JIT do interfejsu użytkownika

Plik CreateUiDefinition.json jest dokładnie taki sam, jak plik interfejsu użytkownika utworzony dla stałego dostępu, z tą różnicą, że należy dołączyć krok, który umożliwia konsumentom włączenie dostępu JIT. Aby dowiedzieć się więcej o publikowaniu pierwszej oferty zarządzanych aplikacji w portalu Azure Marketplace, zobacz [Usługi Azure Managed Applications w portalu Marketplace](publish-marketplace-app.md).

Aby obsługiwać możliwości JIT dla oferty, dodaj następującą zawartość do pliku CreateUiDefinition.json:

W "krokach":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
W "wyjściach":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> Dostęp JIT jest w wersji zapoznawczej. Schemat konfiguracji JIT może ulec zmianie w przyszłych iteracjach.

## <a name="enable-jit-access"></a>Włączanie dostępu JIT

Podczas definiowania oferty na rynku upewnij się, że umożliwiasz dostęp JIT.

1. Zaloguj się do [portalu publikowania partnerów w chmurze](https://cloudpartner.azure.com).

1. Podaj wartości do publikowania aplikacji zarządzanej w portalu Marketplace. Wybierz **tak,** aby **włączyć dostęp JIT?**

   ![Włącz dostęp just-in-time](./media/request-just-in-time-access/marketplace-enable.png)

Dodano krok konfiguracji JIT do interfejsu użytkownika i włączono dostęp JIT w ofercie portalu Marketplace. Gdy konsumenci wdrażają aplikację zarządzaną, mogą [włączyć dostęp JIT dla swojego wystąpienia](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Żądanie dostępu

Gdy trzeba uzyskać dostęp do zasobów zarządzanych konsumenta, należy wysłać żądanie określonej roli, czasu i czasu trwania. Następnie konsument musi zatwierdzić żądanie.

Aby wysłać żądanie dostępu JIT:

1. Wybierz **JIT Access** dla zarządzanej aplikacji, do której chcesz uzyskać dostęp.

1. Wybierz **pozycję Kwalifikujące się role**i wybierz pozycję **Aktywuj** w kolumnie AKCJA dla żądanej roli.

   ![Aktywuj żądanie dostępu](./media/request-just-in-time-access/send-request.png)

1. W formularzu **Aktywuj rolę** wybierz godzinę rozpoczęcia i czas trwania roli, aby twoja rola była aktywna. Wybierz **pozycję Aktywuj,** aby wysłać żądanie.

   ![Aktywuj dostęp](./media/request-just-in-time-access/activate-access.png) 

1. Wyświetl powiadomienia, aby zobaczyć, że nowe żądanie JIT jest pomyślnie wysyłane do konsumenta.

   ![Powiadomienie](./media/request-just-in-time-access/in-progress.png)

   Teraz musisz poczekać, aż konsument [zatwierdzi Twoje żądanie](approve-just-in-time-access.md#approve-requests).

1. Aby wyświetlić stan wszystkich żądań JIT dla aplikacji zarządzanej, wybierz **JIT Access** i **Historia żądań**.

   ![Stan widoku](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Znane problemy

Główny identyfikator konta żądającego dostępu JIT musi być jawnie uwzględniony w definicji aplikacji zarządzanej. Konto nie może być uwzględnione tylko za pośrednictwem grupy, która jest określona w pakiecie. To ograniczenie zostanie ustalone w przyszłej wersji.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o zatwierdzaniu żądań dostępu JIT, zobacz [Zatwierdzanie dostępu just-in-time w aplikacjach zarządzanych platformy Azure.](approve-just-in-time-access.md)
