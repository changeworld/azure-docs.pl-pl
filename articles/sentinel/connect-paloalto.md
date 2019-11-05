---
title: Łączenie danych Palo Alto Networks z platformą Azure Microsoft Docs
description: Dowiedz się, jak łączyć dane Palo Alto Networks z platformą Azure.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: rkarlin
ms.openlocfilehash: 8b1331eb99fd3d061d231ae48c40a721911e74db
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73475846"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Łączenie Palo Alto z platformą Azure — wskaźnik



W tym artykule wyjaśniono, jak połączyć urządzenie z programem Palo Alto Networks z platformą Azure. Łącznik danych Palo Alto Networks umożliwia łatwe łączenie dzienników Palo Alto Networks z platformą Azure, przeglądanie pulpitów nawigacyjnych, tworzenie alertów niestandardowych i ulepszanie badania. Korzystanie z Palo Alto Networks na platformie Azure wskaźnikowa obejmuje więcej szczegółowych informacji na temat użycia Internetu w organizacji i poprawi możliwości operacji zabezpieczeń. 


## <a name="how-it-works"></a>Jak to działa

Należy wdrożyć agenta na dedykowanym komputerze z systemem Linux (maszynie wirtualnej lub lokalnie) do obsługi komunikacji między sieciami Palo Alto i platformą Azure. Na poniższym diagramie opisano konfigurację w przypadku maszyny wirtualnej z systemem Linux na platformie Azure.

 ![CEF na platformie Azure](./media/connect-cef/cef-syslog-azure.png)

Ta konfiguracja będzie również dostępna w przypadku korzystania z maszyny wirtualnej w innej chmurze lub na maszynie lokalnej. 

 ![CEF lokalnie](./media/connect-cef/cef-syslog-onprem.png)


## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami

Upewnij się, że skonfigurowano zabezpieczenia maszyny zgodnie z zasadami zabezpieczeń organizacji. Można na przykład skonfigurować sieć do dopasowania do zasad zabezpieczeń sieci firmowej i zmienić porty i protokoły w demoum, aby dostosować je do swoich wymagań. Aby ulepszyć konfigurację zabezpieczeń komputera, można użyć następujących instrukcji:  [bezpieczna maszyna wirtualna na platformie Azure](../virtual-machines/linux/security-policy.md), [najlepsze rozwiązania dotyczące zabezpieczeń sieci](../security/fundamentals/network-best-practices.md).

Aby można było korzystać z komunikacji TLS między rozwiązaniem zabezpieczeń a maszyną dziennika systemowego, należy skonfigurować demona dziennika systemu (rsyslog lub Dziennik systemowy) do komunikacji w protokole TLS: [szyfrowanie ruchu dziennika systemu przy użyciu protokołu TLS-rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [szyfrowanie komunikatów dzienników przy użyciu protokołu TLS — Dziennik systemowy — ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).

 
## <a name="prerequisites"></a>Wymagania wstępne
Upewnij się, że maszyna z systemem Linux używaną jako serwer proxy ma jeden z następujących systemów operacyjnych:

- 64 — bit
  - CentOS 6 i 7
  - Amazon Linux 2017,09
  - Oracle Linux 6 i 7
  - Red Hat Enterprise Linux Server 6 i 7
  - Debian GNU/Linux 8 i 9
  - Ubuntu Linux 14,04 LTS, 16,04 LTS i 18,04 LTS
  - SUSE Linux Enterprise Server 12
- 32 — bit
   - CentOS 6
   - Oracle Linux 6
   - Red Hat Enterprise Linux Server 6
   - Debian GNU/Linux 8 i 9
   - Ubuntu Linux 14,04 LTS i 16,04 LTS
 
 - Wersje demona
   - Dziennik systemu — NG: 2,1-3.22.1
   - Rsyslog: V8
  
 - Obsługiwane są specyfikacje RFC dziennika systemowego
   - Dziennik systemowy RFC 3164
   - Dziennik systemowy RFC 5424
 
Upewnij się, że komputer spełnia również następujące wymagania: 
- Uprawnienia
    - Musisz mieć podwyższony poziom uprawnień (sudo) na swojej maszynie. 
- Wymagania dotyczące oprogramowania
    - Upewnij się, że na maszynie jest uruchomiony Język Python
## <a name="step-1-deploy-the-agent"></a>Krok 1. wdrażanie agenta

W tym kroku należy wybrać maszynę z systemem Linux, która będzie pełnić rolę serwera proxy między wskaźnikiem danych platformy Azure a rozwiązaniem zabezpieczeń. Na komputerze proxy należy uruchomić skrypt, który:
- Instaluje agenta Log Analytics i konfiguruje go zgodnie z potrzebami, aby nasłuchiwać komunikatów dziennika systemowego na porcie 514 przez TCP i wysyłać komunikaty CEF do obszaru roboczego wskaźnikowego platformy Azure.
- Konfiguruje demona dziennika systemu do przesyłania dalej komunikatów CEF do agenta Log Analytics przy użyciu portu 25226.
- Ustawia agenta dziennika systemowego do zbierania danych i bezpiecznego wysyłania go do Log Analytics, gdzie jest analizowany i wzbogacony.
 
 
1. W portalu wskaźnikowym platformy Azure kliknij pozycję **Łączniki danych** i wybierz pozycję **Palo Alto Networks** , a następnie **Otwórz stronę łącznik**. 

1. W obszarze **Instalowanie i Konfigurowanie agenta dziennika**systemu wybierz typ maszyny, platformę Azure, inną chmurę lub lokalnie. 
   > [!NOTE]
   > Ponieważ skrypt w następnym kroku instaluje agenta Log Analytics i łączy maszynę z obszarem roboczym wskaźnikowego platformy Azure, upewnij się, że ta maszyna nie jest połączona z żadnym innym obszarem roboczym.
1. Musisz mieć podwyższony poziom uprawnień (sudo) na swojej maszynie. Upewnij się, że na maszynie jest zainstalowany program Python, przy użyciu następującego polecenia: `python –version`

1. Uruchom na komputerze proxy następujący skrypt.
   `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]`
1. Gdy skrypt jest uruchomiony, upewnij się, że nie są wyświetlane żadne komunikaty o błędach lub ostrzeżeniach.


 
## <a name="step-2-forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Krok 2. przekazywanie dzienników Palo Alto sieci do agenta dziennika systemu

Skonfiguruj Palo Alto Networks do przesyłania dalej komunikatów dziennika systemowego w formacie CEF do obszaru roboczego platformy Azure za pośrednictwem agenta dziennika systemu:
1.  Przejdź do [przewodnika konfiguracji usługi Common Event format (CEF)](https://docs.paloaltonetworks.com/resources/cef) i Pobierz plik PDF dla danego typu urządzenia. Postępuj zgodnie ze wszystkimi instrukcjami w przewodniku, aby skonfigurować urządzenie Palo Alto Networks do zbierania zdarzeń CEF. 

1.  Przejdź do sekcji [konfigurowanie monitorowania dziennika](https://aka.ms/asi-syslog-paloalto-forwarding) systemowego i wykonaj kroki 2 i 3, aby skonfigurować przekazywanie zdarzeń CEF z urządzenia Palo Alto Networks do usługi Azure wskaźnikowej.

    1. Upewnij się, że **Format serwera dziennika** systemu jest ustawiony na **BSD**.

       > [!NOTE]
       > Operacje kopiowania/wklejania z pliku PDF mogą zmieniać tekst i wstawiać losowe znaki. Aby tego uniknąć, skopiuj tekst do edytora i Usuń wszystkie znaki, które mogą spowodować przerwanie formatowania dziennika przed jego wklejeniem, jak widać w tym przykładzie.
 
        ![Problem z kopiowaniem tekstu CEF](./media/connect-cef/paloalto-text-prob1.png)

2. Aby użyć odpowiedniego schematu w Log Analytics dla zdarzeń Palo Alto Networks, wyszukaj ciąg **CommonSecurityLog**.

## <a name="step-3-validate-connectivity"></a>Krok 3. Weryfikowanie łączności

1. Otwórz Log Analytics, aby upewnić się, że dzienniki są odbierane przy użyciu schematu CommonSecurityLog.<br> Rozpoczęcie wyświetlania dzienników w Log Analytics może zająć więcej niż 20 minut. 

1. Przed uruchomieniem skryptu zalecamy wysłanie komunikatów z rozwiązania zabezpieczeń, aby upewnić się, że są one przekazywane do skonfigurowanego komputera proxy dziennika systemu. 
1. Musisz mieć podwyższony poziom uprawnień (sudo) na swojej maszynie. Upewnij się, że na maszynie jest zainstalowany program Python, przy użyciu następującego polecenia: `python –version`
1. Uruchom następujący skrypt, aby sprawdzić łączność między agentem, wskaźnikiem kontroli platformy Azure i rozwiązaniem zabezpieczeń. Sprawdza, czy przekazanie demona została prawidłowo skonfigurowana, nasłuchuje na prawidłowych portach i nie blokuje komunikacji między demonem a agentem Log Analytics. Skrypt wysyła również komunikat "TestCommonEventFormat" służący do przetestowania kompleksowej łączności. <br>
 `sudo wget https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]`







## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób nawiązywania połączeń z urządzeniami Palo Alto Networks z platformą Azure. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](tutorial-detect-threats-built-in.md).

