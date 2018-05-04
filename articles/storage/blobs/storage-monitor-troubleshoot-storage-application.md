---
title: Monitorowanie i rozwiązywanie problemów aplikacji magazynu w chmurze na platformie Azure | Microsoft Docs
description: Korzystanie z narzędzi diagnostycznych, metryk i alertów w celu rozwiązywania problemów i monitorowania aplikacji w chmurze.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: eb58104309802125a8424cbbf8a1bef3d1c5e79c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-and-troubleshoot-a-cloud-storage-application"></a>Monitorowanie i rozwiązywanie problemów aplikacji magazynu w chmurze

Ten samouczek to czwarta i ostatnia część serii. Zawiera instrukcje monitorowania aplikacji magazynu w chmurze i rozwiązywania problemów.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Włączanie rejestrowania i metryk
> * Włączanie alertów dotyczących błędów autoryzacji
> * Uruchamianie testowego ruchu z nieprawidłowymi tokenami SAS
> * Pobieranie i analizowanie dzienników

[Usługa Azure Storage Analytics](../common/storage-analytics.md) zapewnia dane rejestrowania i metryk dla konta magazynu. Te dane informują o kondycji konta magazynu. Przed uzyskaniem wglądu w konto magazynu należy skonfigurować zbieranie danych. Ten proces polega na włączeniu rejestrowania, skonfigurowaniu metryk i włączeniu alertów.

Rejestrowanie i metryki dotyczące konta magazynu można włączyć na karcie **Diagnostyka** w witrynie Azure Portal. Istnieją dwa typy metryk. **Metryki agregacji** zbierają dane dotyczące ruchu przychodzącego/wychodzącego, dostępności, opóźnień i odsetków powodzenia. Te metryki są agregowane dla obiektów blob, kolejek, tabel i usług plików. **Metryki na interfejs API** zbierają ten sam zestaw metryk dla każdej operacji magazynu w interfejsie API usługi Azure Storage. Rejestrowanie danych magazynu umożliwia zarejestrowanie szczegółów dotyczących żądań zakończonych zarówno powodzeniem, jak i niepowodzeniem na koncie magazynu. Te dzienniki przedstawiają szczegółowe informacje o operacjach odczytu, zapisu i usuwania, wykonanych na tabelach, kolejkach i obiektach blob na platformie Azure. Zawierają także przyczyny niepowodzeń żądań, na przykład przekroczenia limitu czasu, ograniczenia i błędy autoryzacji.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

## <a name="turn-on-logging-and-metrics"></a>Włączanie rejestrowania i metryk

W menu po lewej stronie wybierz kolejno pozycje **Grupy zasobów** i **myResourceGroup**, a następnie wybierz konto magazynu na liście zasobów.

W obszarze **Diagnostyka** zmień ustawienie **Stan** na **Włączone**. Upewnij się, że wszystkie opcje w obszarze **Właściwości obiektu blob** są włączone.

Po zakończeniu kliknij przycisk **Zapisz**.

![Okienko diagnostyki](media/storage-monitor-troubleshoot-storage-application/contoso.png)

## <a name="enable-alerts"></a>Włączanie alertów

Alerty umożliwiają wysłanie wiadomości e-mail do administratora lub wyzwolenie elementu webhook w momencie naruszenia progu przez metrykę. W tym przykładzie włączony zostanie alert dotyczący metryki `SASClientOtherError`.

### <a name="navigate-to-the-storage-account-in-the-azure-portal"></a>Przechodzenie do konta magazynu w witrynie Azure Portal

W menu po lewej stronie wybierz kolejno pozycje **Grupy zasobów** i **myResourceGroup**, a następnie wybierz konto magazynu na liście zasobów.

W sekcji **Monitorowanie** wybierz pozycję **Reguły alertów**.

Wybierz pozycję **+ Dodaj alert**, a następnie w obszarze **Dodawanie reguły alertów** wprowadź wymagane informacje. Wybierz pozycję `SASClientOtherError` z listy rozwijanej **Metryka**.

![Okienko diagnostyki](media/storage-monitor-troubleshoot-storage-application/figure2.png)

## <a name="simulate-an-error"></a>Symulowanie błędu

Aby zasymulować prawidłowy alert, można spróbować zażądać nieistniejącego obiektu blob z konta magazynu. W tym celu zamień wartość `<incorrect-blob-name>` na nieistniejącą wartość. Uruchom następujący kod przykładowy kilka razy, aby zasymulować zakończone niepowodzeniem żądania obiektu blob.

```azurecli-interactive
sasToken=$(az storage blob generate-sas \
    --account-name <storage-account-name> \
    --account-key <storage-account-key> \
    --container-name <container> \
    --name <incorrect-blob-name> \
    --permissions r \
    --expiry `date --date="next day" +%Y-%m-%d` \
    --output tsv)

curl https://<storage-account-name>.blob.core.windows.net/<container>/<incorrect-blob-name>?$sasToken
```

Poniższa ilustracja przedstawia przykładowy alert utworzony na podstawie symulowanego błędu wywołanego w poprzednim przykładzie.

 ![Alert przykładowy](media/storage-monitor-troubleshoot-storage-application/alert.png)

## <a name="download-and-view-logs"></a>Pobieranie i wyświetlanie dzienników

Dane dzienników magazynu są przechowywane w zestawie obiektów blob w kontenerze obiektów blob o nazwie **$logs** na koncie magazynu. Ten kontener nie jest wyświetlany na liście wszystkich kontenerów obiektów blob na koncie, ale można wyświetlić jego zawartość po uzyskaniu do niego bezpośredniego dostępu.

W tym scenariuszu należy użyć narzędzia [Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) do interakcji z kontem usługi Azure Storage.

### <a name="download-microsoft-message-analyzer"></a>Pobieranie narzędzia Microsoft Message Analyzer

Pobierz narzędzie [Microsoft Message Analyzer](https://www.microsoft.com/download/details.aspx?id=44226) i zainstaluj aplikację.

Uruchom aplikację i wybierz kolejno pozycje **File** (Plik)  >  **Open**  (Otwórz)  >  **From Other File Sources** (Z innych źródeł plików).

W oknie dialogowym **File Selector** (Selektor plików) wybierz opcję **+ Add Azure Connection** (+ Dodaj połączenie Azure). Uzupełnij pola **Storage account name** (Nazwa konta magazynu) i **Account key** (Klucz konta), a następnie kliknij przycisk **OK**.

![Narzędzie Microsoft Message Analyzer — Okno dialogowe Dodawanie połączenia z usługą Azure Storage](media/storage-monitor-troubleshoot-storage-application/figure3.png)

Po nawiązaniu połączenia rozwiń kontenery w widoku drzewa magazynu, aby wyświetlić obiekty blob dziennika. Wybierz najnowszy dziennik i kliknij przycisk **OK**.

![Narzędzie Microsoft Message Analyzer — Okno dialogowe Dodawanie połączenia z usługą Azure Storage](media/storage-monitor-troubleshoot-storage-application/figure4.png)

W oknie dialogowym **New Session** (Nowa sesja) kliknij przycisk **Start** (Uruchom), aby wyświetlić dziennik.

Po otwarciu dziennika można wyświetlać zdarzenia magazynu. Jak widać na poniższej ilustracji, na koncie magazynu wystąpiło zdarzenie `SASClientOtherError`. Aby uzyskać dodatkowe informacje na temat rejestrowania danych magazynu, odwiedź stronę [Storage Analytics (Analityka magazynu)](../common/storage-analytics.md).

![Narzędzie Microsoft Message Analyzer — wyświetlanie zdarzeń](media/storage-monitor-troubleshoot-storage-application/figure5.png)

[Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) to kolejne narzędzie, które może służyć do interakcji z kontami magazynu, w tym z kontenerem **$logs** i dziennikami, które są w nim zawarte.

## <a name="next-steps"></a>Następne kroki

W czwartej i ostatniej części serii przedstawiono sposób monitorowania konta magazynu i rozwiązywania problemów, w tym następujące czynności:

> [!div class="checklist"]
> * Włączanie rejestrowania i metryk
> * Włączanie alertów dotyczących błędów autoryzacji
> * Uruchamianie testowego ruchu z nieprawidłowymi tokenami SAS
> * Pobieranie i analizowanie dzienników

Kliknij ten link, aby wyświetlić wstępnie skompilowane przykładowe skrypty dla usługi Storage.

> [!div class="nextstepaction"]
> [Azure storage script samples (Przykładowe skrypty dla usługi Azure Storage)](storage-samples-blobs-cli.md)