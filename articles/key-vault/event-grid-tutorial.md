---
title: Odbieraj powiadomienia magazynu kluczy i odpowiadaj na nie za pomocą Azure Event Grid
description: Dowiedz się, jak zintegrować Key Vault z Azure Event Grid.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 56490ede89a5859ef73d9110b46ea55fd9b96d54
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033555"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Instrukcje: odbieranie powiadomień z magazynu kluczy i odpowiadanie na nie Azure Event Grid (wersja zapoznawcza)

Key Vault integracja z usługą Azure Event Grid, obecnie dostępna w wersji zapoznawczej, umożliwia użytkownikom otrzymywanie powiadomień o zmianie stanu wpisu tajnego przechowywanego w magazynie kluczy. Aby zapoznać się z omówieniem tej funkcji, zobacz [monitorowanie Key Vault z Azure Event Grid](event-grid-overview.md).

W tym przewodniku pokazano, jak odbierać Key Vault powiadomienia za pomocą Azure Event Grid i jak reagować na zmiany stanu przy użyciu Azure Automation.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Magazyn kluczy w ramach subskrypcji platformy Azure. Nowy magazyn kluczy można szybko utworzyć, wykonując kroki z [zestawu i pobierając klucz tajny z Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)

## <a name="concepts"></a>Pojęcia

Azure Event Grid to usługa obsługi zdarzeń dla chmury. W tym przewodniku zasubskrybujesz zdarzenia dla magazynu kluczy i skierujesz zdarzenia do Azure Automation. Gdy jeden z wpisów tajnych w magazynie kluczy wkrótce wygaśnie, Event Grid zostanie powiadomiona o zmianie stanu i spowoduje WYSŁANie żądania HTTP do punktu końcowego. Element webhook wyzwala Azure Automation wykonywanie skryptu programu PowerShell.

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation

Utwórz konto Azure Automation przy użyciu [Azure Portal](https://portal.azure.com).

1.  Przejdź do portal.azure.com i zaloguj się do swojej subskrypcji.

1.  W polu wyszukiwania wpisz ciąg "konta usługi Automation".

1.  W sekcji "usługi" listy rozwijanej na pasku wyszukiwania wybierz pozycję "konta usługi Automation".

1.  Kliknij pozycję Add (Dodaj).

    ![](media/image2.png)

1.  Wypełnij wymagane informacje w bloku "Dodawanie konta usługi Automation" i wybierz pozycję "Utwórz".

## <a name="create-a-runbook"></a>Tworzenie elementu Runbook

Gdy konto Azure Automation będzie gotowe, Utwórz element Runbook.

![](media/image3.png)

1.  Wybierz właśnie utworzone konto usługi Automation.

1.  Wybierz pozycję "elementy Runbook" w sekcji Automatyzacja procesów.

1.  Kliknij pozycję "Utwórz element Runbook".

1.  Nadaj nazwę elementowi Runbook i wybierz pozycję "PowerShell" jako typ elementu Runbook.

1.  Kliknij utworzony element Runbook, a następnie wybierz przycisk "Edytuj".

1.  Wprowadź następujący kod (na potrzeby testowania), a następnie kliknij przycisk "Publikuj". Spowoduje to wyjście z otrzymanego wyniku żądania POST.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![](media/image4.png)

## <a name="create-a-webhook"></a>Tworzenie elementu webhook

Teraz Utwórz element webhook, aby wyzwolić nowo utworzony element Runbook.

1.  Wybierz pozycję "elementy webhook" z sekcji Resources elementu Runbook, który właśnie został opublikowany.

1.  Kliknij pozycję "Dodaj element webhook".

    ![](media/image5.png)

1.  Wybierz pozycję "Utwórz nowy element webhook".

1. Nadaj nazwę elementowi webhook, ustaw datę wygaśnięcia i **Skopiuj adres URL**.

    > [!IMPORTANT] 
    > Po utworzeniu adresu URL nie można go wyświetlić. Pamiętaj, aby zapisać kopię bezpiecznej lokalizacji, w której można uzyskać do niej dostęp w pozostałej części tego przewodnika.

1. Kliknij pozycję "parametry i ustawienia uruchomieniowe", a następnie wybierz pozycję "OK". Nie należy wprowadzać żadnych parametrów. Spowoduje to włączenie przycisku "Utwórz".

1. Wybierz pozycję "OK" i wybierz pozycję "Utwórz".

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Tworzenie subskrypcji usługi Event Grid

Utwórz subskrypcję Event Grid przy użyciu [Azure Portal](https://portal.azure.com).

1.  Otwórz Azure Portal przy użyciu następującego linku: https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  Przejdź do magazynu kluczy i wybierz kartę "zdarzenia". Jeśli nie widzisz karty zdarzenia, upewnij się, że korzystasz z [wersji zapoznawczej portalu](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![](media/image7.png)

1.  Kliknij przycisk "+ subskrypcja zdarzeń".

1.  Utwórz opisową nazwę subskrypcji.

1.  Wybierz pozycję "Event Grid schemat".

1.  "Zasób tematu" powinien być magazynem kluczy, który ma być monitorowany pod kątem zmian stanu.

1.  Dla opcji "Filtruj do typów zdarzeń" pozostaw wszystkie zaznaczone ("9 wybrane").

1.  W obszarze "typ punktu końcowego" Wybierz pozycję "element webhook".

1.  Wybierz pozycję "Wybierz punkt końcowy". W okienku nowy kontekst Wklej adres URL elementu webhook z kroku [Utwórz element webhook](#create-a-webhook) w polu "punkt końcowy subskrybenta".

1.  W okienku kontekstowym wybierz pozycję "Potwierdź wybór".

1.  Wybierz pozycję "Utwórz".

    ![](media/image8.png)

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

Sprawdź, czy Twoja subskrypcja Event Grid jest skonfigurowana.  W tym teście przyjęto założenie, że zasubskrybowano powiadomienie "wpis tajny nowej wersji" w ramach [tworzenia subskrypcji usługi Event Grid](#create-an-event-grid-subscription)i że masz odpowiednie uprawnienia do tworzenia nowej wersji wpisu tajnego w magazynie kluczy.

![](media/image9.png)

![](media/image10.png)

1.  Przejdź do magazynu kluczy na Azure Portal

1.  Utwórz nowy wpis tajny. Dla celów testowych ustaw wartość Data wygaśnięcia na następny dzień.

1.  Przejdź do karty zdarzenia w magazynie kluczy.

1.  Wybierz utworzoną subskrypcję usługi Event Grid.

1.  W obszarze metryki Sprawdź, czy zdarzenie zostało przechwycone. Oczekiwane są dwa zdarzenia: SecretNewVersion i SecretNearExpiry. Spowoduje to sprawdzenie, czy siatka zdarzeń pomyślnie przechwyciła zmianę stanu wpisu tajnego w magazynie kluczy.

    ![](media/image11.png)

1.  Przejdź do swojego konta Azure Automation.

1.  Wybierz kartę "elementy Runbook" i wybierz utworzony element Runbook.

1.  Wybierz kartę "elementy webhook" i upewnij się, że sygnatura czasowa "Ostatnia wyzwolona" mieści się w przedziale od 60 sekund od momentu utworzenia nowego klucza tajnego.  To potwierdza, że Event Grid wprowadził wpis do elementu webhook ze szczegółami zdarzenia zmiany stanu w magazynie kluczy, a element webhook został wyzwolony.

    ![](media/image12.png)

1. Wróć do elementu Runbook i wybierz kartę "przegląd".

1. Zapoznaj się z listą ostatnich zadań. Powinno się zobaczyć, że zadanie zostało utworzone i że stan jest zakończony.  Pozwala to upewnić się, że element webhook wyzwolił elementu Runbook, aby rozpocząć wykonywanie jego skryptu.

    ![](media/image13.png)

1. Wybierz ostatnie zadanie i sprawdź żądanie POST wysłane z usługi Event Grid do elementu webhook. Sprawdź kod JSON i upewnij się, że parametry magazynu kluczy i typu zdarzenia są poprawne. Jeśli parametr "typ zdarzenia" w obiekcie JSON pasuje do zdarzenia, które wystąpiło w magazynie kluczy (w tym przykładzie Microsoft. Key SecretNearExpiry) test zakończył się pomyślnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unable-to-create-event-subscription"></a>Nie można utworzyć subskrypcji zdarzeń

Zarejestruj Event Grid i Key Vault dostawcę w dostawcach zasobów subskrypcji platformy Azure. Zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Następne kroki

Gratulacje! Jeśli wykonano wszystkie powyższe kroki, możesz teraz przystąpić do programistycznego reagowania na zmiany stanu wpisów tajnych przechowywanych w magazynie kluczy.

Jeśli używasz systemu opartego na sondowaniu do wyszukiwania zmian stanu wpisów tajnych w magazynie kluczy, Przeprowadź migrację do korzystania z tej funkcji powiadomień. Możesz również zastąpić skrypt testowy w elemencie Runbook za pomocą kodu, aby programowo odnowić wpisy tajne, gdy wkrótce wygasną.

Dowiedz się więcej:

- [Przegląd Azure Key Vault](key-vault-overview.md)
- [Przegląd Azure Event Grid](../event-grid/overview.md)
- [Key Vault monitorowania z Azure Event Grid (wersja zapoznawcza)](event-grid-overview.md)
- [Schemat zdarzeń Azure Event Grid dla Azure Key Vault (wersja zapoznawcza)](../event-grid/event-schema-key-vault.md)
- [Omówienie usługi Azure Automation](../automation/index.yml)