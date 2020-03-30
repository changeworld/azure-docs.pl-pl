---
title: Rozwiązywanie problemów z opóźnieniami przy użyciu dzienników Analityki magazynu
description: Identyfikowanie i rozwiązywanie problemów z opóźnieniami przy użyciu dzienników analitycznych usługi Azure Storage i optymalizowanie aplikacji klienckiej.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196521"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Rozwiązywanie problemów z opóźnieniami przy użyciu dzienników Analityki magazynu

Diagnozowanie i rozwiązywanie problemów jest kluczową umiejętnością tworzenia i obsługi aplikacji klienckich za pomocą usługi Azure Storage.

Ze względu na rozproszony charakter aplikacji platformy Azure diagnozowanie i rozwiązywanie problemów z błędami i wydajnością może być bardziej złożone niż w tradycyjnych środowiskach.

W poniższych krokach pokazano, jak zidentyfikować i rozwiązać problemy z opóźnieniami przy użyciu dzienników analitycznych usługi Azure Storage i zoptymalizować aplikację kliencką.

## <a name="recommended-steps"></a>Zalecane czynności

1. Pobierz [dzienniki analizy pamięci masowej](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Użyj następującego skryptu programu PowerShell, aby przekonwertować dzienniki formatu nieprzetworzonego na format tabelaryczny:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Skrypt uruchomi okno graficznego interfejsu użytkownika, w którym można filtrować informacje według kolumn, jak pokazano poniżej.

   ![Okno analizatora dziennika analitycznego magazynu](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Zawęź wpisy dziennika na podstawie "typu operacji" i poszukaj wpisu dziennika utworzonego w przedziale czasowym problemu.

   ![Wpisy dziennika typu operacji](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. W czasie, gdy wystąpił problem, ważne są następujące wartości:

   * Typ operacji = GetBlob
   * status żądania = SASNetworkError
   * End-to-End-Latency-In-Ms = 8453
   * Serwer-opóźnienie w ms = 391

   Opóźnienie end-to-end jest obliczane przy użyciu następującego równania:

   * Opóźnienie typu end-to-end = opóźnienie serwera + opóźnienie klienta

   Oblicz opóźnienie klienta przy użyciu wpisu dziennika:

   * Opóźnienie klienta = opóźnienie typu "end-to-end" — opóźnienie serwera

          * Example: 8453 – 391 = 8062ms

   Poniższa tabela zawiera informacje o wynikach Operacji o dużym opóźnieniu i RequestStatus:

   |   |RequestStatus=<br>Powodzenie|RequestStatus=<br>(SAS) Usługa NetworkError|Zalecenie|
   |---|---|---|---|
   |GetBlob ( GetBlob )|Tak|Nie|[**Operacja GetBlob:** RequestStatus = Sukces](#getblob-operation-requeststatus--success)|
   |GetBlob ( GetBlob )|Nie|Tak|[**Operacja GetBlob:** RequestStatus = (SAS)NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob (łań.)|Tak|Nie|[**Operacja put:** RequestStatus = Sukces](#put-operation-requeststatus--success)|
   |PutBlob (łań.)|Nie|Tak|[**Operacja put:** RequestStatus = (SAS)NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Wyniki stanu

### <a name="getblob-operation-requeststatus--success"></a>Operacja GetBlob: RequestStatus = Sukces

Sprawdź następujące wartości wymienione w kroku 5 sekcji "Zalecane kroki":

* Opóźnienie typu end-to-end
* Opóźnienie serwera
* Opóźnienie klienta

W **operacji GetBlob** z **RequestStatus = Sukces**, jeśli **max czas** jest spędzany w **matrzeńc klienta,** oznacza to, że usługa Azure Storage spędza dużą ilość czasu zapisywania danych do klienta. To opóźnienie wskazuje problem po stronie klienta.

**Zalecenie:**

* Zbadaj kod w kliencie.
* Użyj Wireshark, Microsoft Message Analyzer lub Tcping do zbadania problemów z łącznością sieciową z klienta. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operacja GetBlob: RequestStatus = (SAS)NetworkError

Sprawdź następujące wartości wymienione w kroku 5 sekcji "Zalecane kroki":

* Opóźnienie typu end-to-end
* Opóźnienie serwera
* Opóźnienie klienta

W **operacji GetBlob** z **RequestStatus = (SAS)NetworkError**, jeśli **max time** jest spędzany w **kliencie-latencji**, najczęstszym problemem jest to, że klient rozłącza się przed wygaśnięciem limitu czasu w usłudze magazynu.

**Zalecenie:**

* Zbadaj kod w kliencie, aby zrozumieć, dlaczego i kiedy klient rozłącza się z usługą magazynu.
* Użyj Wireshark, Microsoft Message Analyzer lub Tcping do zbadania problemów z łącznością sieciową z klienta. 

### <a name="put-operation-requeststatus--success"></a>Operacja put: RequestStatus = Sukces

Sprawdź następujące wartości wymienione w kroku 5 sekcji "Zalecane kroki":

* Opóźnienie typu end-to-end
* Opóźnienie serwera
* Opóźnienie klienta

W **operacji put** z **RequestStatus = Sukces**, jeśli max **czas** jest spędzany w **matrzeńcem klienta,** oznacza to, że klient zajmuje więcej czasu na wysyłanie danych do usługi Azure Storage. To opóźnienie wskazuje problem po stronie klienta.

**Zalecenie:**

* Zbadaj kod w kliencie.
* Użyj Wireshark, Microsoft Message Analyzer lub Tcping do zbadania problemów z łącznością sieciową z klienta. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operacja umieszczania: RequestStatus = (SAS)NetworkError

Sprawdź następujące wartości wymienione w kroku 5 sekcji "Zalecane kroki":

* Opóźnienie typu end-to-end
* Opóźnienie serwera
* Opóźnienie klienta

W **operacji PutBlob** z **RequestStatus = (SAS)NetworkError**, jeśli **max time** jest spędzany w **kliencie-latencji**, najczęstszym problemem jest to, że klient rozłącza się przed wygaśnięciem limitu czasu w usłudze magazynu.

**Zalecenie:**

* Zbadaj kod w kliencie, aby zrozumieć, dlaczego i kiedy klient rozłącza się z usługą magazynu.
* Użyj Wireshark, Microsoft Message Analyzer lub Tcping do zbadania problemów z łącznością sieciową z klienta.

