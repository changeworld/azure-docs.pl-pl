---
title: Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory
description: Dowiedz się, jak rozwiązywać problemy z nieobsługiwanym środowiskiem Integration Runtime w Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907284"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>Rozwiązywanie problemów z własnym hostowanym środowiskiem Integration Runtime

W tym artykule przedstawiono typowe metody rozwiązywania problemów z własnym hostowanym środowiskiem Integration Runtime w Azure Data Factory.

## <a name="common-errors-and-resolutions"></a>Typowe błędy i rozwiązania

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>Komunikat o błędzie: własne środowisko Integration Runtime nie może nawiązać połączenia z usługą w chmurze.

- **Objaw**: 

    ![Problem z nieobsługiwanym połączeniem IR](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **Przyczyna**: własne środowisko Integration Runtime nie może nawiązać połączenia z usługą Data Factory (zaplecza). Najczęściej, niż jest to spowodowane przez ustawienia sieci w zaporze.

- **Rozdzielczość**: 

    1. Sprawdź, czy usługa systemu Windows "Integration Runtime Service" jest uruchomiona.
    
        ![Stan działania usługi IR samoobsługowego](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. Jeśli usługa systemu Windows, jak pokazano w [1] jest uruchomiona, postępuj zgodnie z poniższymi instrukcjami:

        1. Jeśli "proxy" nie jest skonfigurowany w ramach własnego środowiska Integration Runtime (ustawienia domyślne nie są konfiguracją serwera proxy), uruchom poniższe polecenie programu PowerShell na komputerze, na którym zainstalowano własne środowisko Integration Runtime: 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > Adres URL usługi może się różnić w zależności od lokalizacji fabryki danych. Adres URL usługi można znaleźć w obszarze interfejsu użytkownika funkcji ADF-> połączenia — > środowiska Integration Runtime — > edytować węzły podczerwieni->, > wyświetlić adresy URL usługi.
            
            Poniżej znajduje się oczekiwana odpowiedź:
            
            ![Odpowiedź polecenia programu PowerShell](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            Jeśli odpowiedź jest inna, należy postępować zgodnie z poniższymi instrukcjami:
            
            * Jeśli zostanie wyświetlony komunikat o błędzie "nie można rozpoznać nazwy zdalnej", występuje problem z usługą DNS. Skontaktuj się z zespołem sieci, aby uzyskać problem z rozpoznawaniem nazw DNS. 
            * Jeśli zostanie wyświetlony komunikat o błędzie "certyfikat SSL/TLS nie jest zaufany", sprawdź, czy certyfikat "https://wu2.frontend.clouddatahub.net/" jest zaufany na maszynie, Zainstaluj certyfikat publiczny za pomocą Menedżera certyfikatów, który powinien wyeliminować ten problem.
            * Sprawdź Podgląd zdarzeń w systemie Windows > (dzienniki) — > Dzienniki aplikacji i usług — > Integration Runtime dla każdego błędu, w związku z tym, że jest to spowodowane przez system DNS, regułę zapory i ustawienia sieciowe firmy (wymuszone zamknięcie połączenia). Aby rozwiązać ten problem, należy skontaktować się z zespołem sieciowym w celu uzyskania dalszych Troubleshot, ponieważ każda firma ma dostosowane ustawienia sieci.

        2. Jeśli serwer proxy został skonfigurowany w ramach własnego środowiska Integration Runtime, sprawdź, czy na serwerze proxy jest możliwy dostęp do naszego punktu końcowego usługi. W przypadku przykładowego polecenia odwołaj się do [niego](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies).    
                
            ```powershell
            $user = $env:username
            $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
            Settings').ProxyServer
            $pwd = Read-Host "Password?" -assecurestring
            $proxy = new-object System.Net.WebProxy
            $proxy.Address = $webproxy
            $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
            $proxy.credentials = $account
            $url = "https://wu2.frontend.clouddatahub.net/"
            $wc = new-object system.net.WebClient
            $wc.proxy = $proxy
            $webpage = $wc.DownloadData($url)
            $string = [System.Text.Encoding]::ASCII.GetString($webpage)
            $string
            ```

            Poniżej znajduje się oczekiwana odpowiedź:
            
            ![Odpowiedź polecenia programu PowerShell 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > Zagadnienia dotyczące serwerów proxy:
            > * Sprawdź, czy serwer proxy wymaga listy dozwolonych. Jeśli tak, należy mieć [te domeny](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network) listy dozwolonych.
            > * Sprawdź, czy certyfikat TLS/SSL dla "wu2.frontend.clouddatahub.net/" jest zaufany na serwerze proxy.
            > * Jeśli używasz uwierzytelniania w usłudze Active Directory na serwerze proxy, Zmień konto usługi na konto użytkownika, które ma dostęp do serwera proxy jako "Integration Runtime usługi".

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>Komunikat o błędzie: własny Host Integration Runtime Node/Logical SHIR jest w stanie nieaktywny/"uruchomiony (ograniczony)"

- **Przyczyna**: w stanie nieaktywnym można zobaczyć własny HOSTOWANY węzeł IR, jak pokazano na poniższym zrzucie ekranu:

    ![Nieaktywny samoobsługowy węzeł IR](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    Dzieje się tak, gdy węzły nie mogą komunikować się ze sobą. 

- **Rozdzielczość**: 

    Zaloguj się do hostowanej maszyny wirtualnej węzła i Otwórz widok zdarzeń w obszarze Dzienniki aplikacji i usług — > Integration Runtime, odfiltruj wszystkie dzienniki błędów. 

     1. Jeśli dziennik błędów zawiera: 
    
        **Dziennik błędów**: System. ServiceModel. EndpointNotFoundException: nie można nawiązać połączenia z net. TCP://xxxxxxx.bwld.com: 8060/ExternalService. svc/workermanager. Podjęto próbę nawiązania połączenia dla przedziału czasu 00:00:00.9940994. Kod błędu TCP 10061: nie można nawiązać połączenia, ponieważ maszyna docelowa aktywnie odrzuciła 10.2.4.10:8060.  ---> System .NET. Sockets. SocketException: nie można nawiązać połączenia, ponieważ maszyna docelowa aktywnie odrzuciła 10.2.4.10:8060
    
           w systemie .NET. Sockets. Socket. DoConnect (EndPoint endPointSnapshot, SocketAddress socketAddress)
           
           w systemie .NET. Sockets. Socket. Connect (punkt końcowy remoteEP)
           
           w System. ServiceModel. Channels. SocketConnectionInitiator. Connect (URI URI, limit czasu TimeSpan)
    
        **Rozwiązanie:** Uruchom wiersz polecenia: telnet 10.2.4.10 8060
        
        Jeśli wystąpi Poniższy błąd, skontaktuj się z działem IT złoczyńców, aby uzyskać pomoc dotyczącą rozwiązania tego problemu. Po pomyślnym zalogowaniu się w programie Telnet skontaktuj się z pomocą techniczną firmy Microsoft, jeśli nadal występują problemy ze stanem węzła IR.
        
        ![Błąd wiersza polecenia](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. Jeśli dziennik błędów zawiera:
     
        **Dziennik błędów:** Nie można nawiązać połączenia z menedżerem procesów roboczych: net. TCP://xxxxxx: 8060/ExternalService. svc/Brak wpisów DNS dla hosta azranlcir01r1. Żaden taki host nie jest znany szczegóły wyjątku: System. ServiceModel. EndpointNotFoundException: Brak wpisów DNS dla hosta XXXXX. ---> System .NET. Sockets. SocketException: żaden host jest nieznany w systemie .NET. DNS. GetAddrInfo (Nazwa ciągu) w systemie .NET. DNS. InternalGetHostByName (ciąg nazwy hosta, wartość logiczna includeIPv6) w systemie .NET. DNS. GetHostEntry (ciąg hostNameOrAddress ) w systemie System. ServiceModel. Channels. DnsCache. Rozwiązuj (URI URI)---końcu śladu stosu wyjątków wewnętrznych---śledzenia stosu serwera: w System. ServiceModel. Channels. DnsCache. Rozwiąż (identyfikator URI URI) 
    
        **Rozwiązanie:** Jedno z poniższych dwóch akcji może pomóc w rozwiązaniu problemu:
         1. Umieść wszystkie węzły w tej samej domenie.
         2. Dodaj adres IP do mapowania hosta we wszystkich hostach hostowanej maszyny wirtualnej.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog dotyczący usługi Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo na platformie Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Przewodnik dotyczący wydajności przepływów danych dla mapowania ADF](concepts-data-flow-performance.md)
