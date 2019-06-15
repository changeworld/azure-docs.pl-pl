---
title: Włączanie i żądania dostępu just in time dla usługi Azure Managed Applications
description: W tym artykule opisano, jak wydawcy usługi Azure Managed Applications żądania dostępu just in time do aplikacji zarządzanej.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66481777"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Włączanie i żądania dostępu just in time dla usługi Azure Managed Applications

Użytkownicy twojej aplikacji zarządzanej może być zapewniony udzielić trwałego dostępu do zarządzanej grupy zasobów. Jako wydawca aplikacji manager możesz preferować konsumenci wiedzieli, dokładnie gdy wymagany jest dostęp do zasobów zarządzanych. Aby dać konsumentów większą kontrolę nad udzielanie dostępu do zasobów zarządzanych usługi Azure Managed Applications udostępnia funkcję dostępu just in time (JIT), która jest obecnie dostępna w wersji zapoznawczej.

Dostęp JIT pozwala na żądanie z podwyższonym poziomem uprawnień dostępu do zasobów zarządzanych aplikacji do rozwiązywania problemów lub konserwacji. Zawsze mieć dostęp tylko do odczytu do zasobów, ale dla określonego przedziału czasu może mieć większy dostęp.

Przepływ pracy do przyznawania dostępu jest następujący:

1. Dodaj aplikację zarządzaną w witrynie marketplace i określ, czy dostęp JIT jest dostępny.

1. Podczas wdrażania usługa konsumenta umożliwia dostęp JIT dla tego wystąpienia aplikacji zarządzanej.

1. Po wdrożeniu konsumenta można zmienić ustawienia dostępu JIT.

1. Wysyłasz żądanie dostępu, gdy trzeba rozwiązywać problemy lub aktualizowanie zarządzanych zasobów.

1. Konsument zatwierdzi żądanie.

Ten artykuł koncentruje się na akcje, które wydawców podjąć Włącz dostęp JIT do przesyłania żądań. Aby dowiedzieć się więcej na temat zatwierdzania żądania dostępu JIT, zobacz [zatwierdzać dostęp just in time w usłudze Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Dodawanie kroku dostępu JIT do interfejsu użytkownika

Plik CreateUiDefinition.json jest tak samo jak utworzonym pliku interfejsu użytkownika dla trwałego dostępu, z wyjątkiem musi zawierać kroku, który umożliwia konsumentów, Włącz dostęp JIT. Aby dowiedzieć się więcej na temat publikowania pierwszej aplikacji zarządzanych oferty w portalu Azure Marketplace, zobacz [usługi Azure Managed Applications na rynku](publish-marketplace-app.md).

Obsługa możliwości JIT dla oferty, dodaj następującą zawartość do pliku CreateUiDefinition.json:

W "kroki":

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
 
W "wyjściowe":

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> Dostęp JIT jest dostępna w wersji zapoznawczej. Schemat konfiguracji JIT może ulec zmianie w przyszłych iteracji.

## <a name="enable-jit-access"></a>Włącz dostęp JIT

Podczas definiowania oferty w portalu marketplace, upewnij się, że możesz włączyć dostęp JIT.

1. Zaloguj się do [Cloud Partner portalu wydawców](https://cloudpartner.azure.com).

1. Należy podać wartości do publikowania aplikacji zarządzanej na rynku. Wybierz **tak** dla **Włącz dostęp JIT?**

   ![Włącz dostęp just in time](./media/request-just-in-time-access/marketplace-enable.png)

Do interfejsu użytkownika dodano kroku konfiguracji JIT i włączono dostęp JIT w ofert portalu marketplace. Konsumenci wdrażania aplikacji zarządzanej, mogą oni [Włącz dostęp JIT do wystąpienia](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Żądaj dostępu

Jeśli potrzebujesz dostępu do zasobów zarządzanych konsumenta, możesz wysłać żądanie określoną rolę, czas i czas trwania. Użytkownik musi następnie zatwierdź żądanie.

Aby wysłać żądanie dostępu JIT:

1. Wybierz **dostępu JIT do** wymagany jest dostęp do aplikacji zarządzanej.

1. Wybierz **kwalifikujące się role**i wybierz **Aktywuj** w kolumnie akcji dla roli, ma.

   ![Aktywuj żądanie dostępu](./media/request-just-in-time-access/send-request.png)

1. Na **aktywować rolę** wybierz godzinę rozpoczęcia i czas trwania dla Twojej roli jako aktywny. Wybierz **Aktywuj** Aby wysłać żądanie.

   ![Aktywowanie dostępu do](./media/request-just-in-time-access/activate-access.png) 

1. Wyświetl powiadomienia, aby zobaczyć, że nowe żądanie JIT pomyślnie jest wysyłany do użytkownika.

   ![Powiadomienia](./media/request-just-in-time-access/in-progress.png)

   Teraz należy poczekać, aby [zatwierdzać swojego żądania](approve-just-in-time-access.md#approve-requests).

1. Aby wyświetlić stan wszystkich żądań JIT dla zarządzanych aplikacji, wybierz pozycję **dostępu JIT do** i **historii żądań**.

   ![Wyświetl stan](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Znane problemy

Identyfikator podmiotu zabezpieczeń konta żądanie dostępu JIT, muszą być jawnie uwzględnione w definicji aplikacji zarządzanej. To konto nie może być tylko dołączone za pośrednictwem grupy, który jest określony w pakiecie. To ograniczenie zostanie rozwiązany w przyszłej wersji.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat zatwierdzania żądania dostępu JIT, zobacz [zatwierdzać dostęp just in time w usłudze Azure Managed Applications](approve-just-in-time-access.md).
