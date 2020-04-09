---
title: Przewodnik dotyczący rozwiązywania problemów z usługą Azure Service Bus | Dokumenty firmy Microsoft
description: Ten artykuł zawiera listę wyjątków obsługi wiadomości usługi Azure Service Bus i sugerowane akcje do wykonania w przypadku wystąpienia wyjątku.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: aschhab
ms.openlocfilehash: 63bf035d4e19cc1d64998a6ad533812e71ee71b8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887777"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Przewodnik dotyczący rozwiązywania problemów z usługą Azure Service Bus
Ten artykuł zawiera porady dotyczące rozwiązywania problemów i zalecenia dotyczące kilku problemów, które mogą pojawić się podczas korzystania z usługi Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemy z łącznością, certyfikatem lub limitem czasu
Poniższe kroki mogą pomóc w rozwiązywaniu problemów z łącznością/certyfikatem/limitem czasu dla wszystkich usług w obszarze *.servicebus.windows.net. 

- Przejdź do lub [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/`. Pomaga sprawdzić, czy masz filtrowanie IP lub problemy z siecią wirtualną lub łańcuchem certyfikatów (najczęściej podczas korzystania z java SDK).

    Przykład pomyślnej wiadomości:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Przykład komunikatu o błędzie błędu:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Uruchom następujące polecenie, aby sprawdzić, czy jakikolwiek port jest zablokowany na zaporze. Używane porty to 443 (HTTPS), 5671 (AMQP) i 9354 (Net Messaging/SBMP). W zależności od używanej biblioteki używane są również inne porty. Oto przykładowe polecenie, które sprawdza, czy port 5671 jest zablokowany. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    W systemie Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- W przypadku wystąpienia sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy istnieją jakieś porzucone pakiety. To polecenie spróbuje ustanowić 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie można sprawdzić, ile z nich zakończyło się pomyślnie/nie powiodło się, a także wyświetlić opóźnienie połączenia TCP. Możesz pobrać `psping` narzędzie [stąd](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Można użyć równoważnych poleceń, jeśli używasz `tnc` `ping`innych narzędzi, takich jak , i tak dalej. 
- Uzyskaj śledzenie sieci, jeśli poprzednie kroki nie pomagają i analizować go za pomocą narzędzi, takich jak [Wireshark](https://www.wireshark.org/). W razie potrzeby skontaktuj się z [pomocą techniczną firmy Microsoft.](https://support.microsoft.com/) 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemy, które mogą wystąpić podczas uaktualniania/ponownego uruchamiania usługi
Uaktualnienia i ponowne uruchomienie usługi wewnętrznej bazy danych mogą mieć następujący wpływ na aplikacje:

- Żądania mogą być chwilowo ograniczone.
- Może wystąpić spadek przychodzących wiadomości/żądań.
- Plik dziennika może zawierać komunikaty o błędach.
- Aplikacje mogą być odłączone od usługi na kilka sekund.

Jeśli kod aplikacji korzysta z SDK, zasady ponawiania próby jest już wbudowany i aktywny. Aplikacja połączy się ponownie bez znaczącego wpływu na aplikację/przepływ pracy.

## <a name="unauthorized-access-send-claims-are-required"></a>Nieautoryzowany dostęp: Wymagane są oświadczenia o wysyłaniu
Ten błąd może wystąpić podczas próby uzyskania dostępu do tematu usługi Service Bus z programu Visual Studio na komputerze lokalnym przy użyciu tożsamości zarządzanej przypisanej przez użytkownika z uprawnieniami wysyłania.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

Aby rozwiązać ten problem, należy zainstalować bibliotekę [microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie rozwoju lokalnego](..\key-vault\service-to-service-authentication.md#local-development-authentication). 

Aby dowiedzieć się, jak przypisać uprawnienia do ról, zobacz [Uwierzytelnij tożsamość zarządzaną za pomocą usługi Azure Active Directory, aby uzyskać dostęp do zasobów usługi Azure Service Bus](service-bus-managed-service-identity.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Wyjątki usługi Azure Resource Manager](service-bus-resource-manager-exceptions.md). Lista wyjątków generowanych podczas interakcji z usługą Azure Service Bus przy użyciu usługi Azure Resource Manager (za pomocą szablonów lub bezpośrednich wywołań).
- [Wyjątki obsługi wiadomości](service-bus-messaging-exceptions.md). Zawiera listę wyjątków generowanych przez platformę .NET Framework dla usługi Azure Service Bus. 

