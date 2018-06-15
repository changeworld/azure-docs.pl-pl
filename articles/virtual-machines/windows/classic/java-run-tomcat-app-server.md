---
title: Uruchom serwer aplikacji Java na klasycznej maszyny Wirtualnej Azure
description: W tym samouczku są używane zasoby utworzone za pomocą klasycznym modelu wdrażania i przedstawiono sposób tworzenia maszyny wirtualnej systemu Windows i jest skonfigurowana do uruchamiania Apache Tomcat serwer aplikacji.
services: virtual-machines-windows
documentationcenter: java
author: rmcmurray
manager: mbaldwin
tags: azure-service-management
ms.assetid: d627aa09-f7d6-4239-8110-f8fc5111b939
ms.service: virtual-machines-windows
ms.workload: web
ms.tgt_pltfrm: vm-windows
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: e13228a707e7dae4a4c2505154d01215c40b4716
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31528032"
---
# <a name="how-to-run-a-java-application-server-on-a-virtual-machine-created-with-the-classic-deployment-model"></a>Jak uruchomić serwer aplikacji Java na maszynie wirtualnej utworzonej przy użyciu klasycznego modelu wdrażania
> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule omówiono przy użyciu klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dla szablonu usługi Resource Manager do wdrażania aplikacji sieci Web Java 8 i Tomcat, zobacz [tutaj](https://azure.microsoft.com/documentation/templates/201-web-app-java-tomcat/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Przy użyciu platformy Azure maszyny wirtualnej służy do nadania możliwości serwera. Na przykład można skonfigurować maszynę wirtualną działającą na platformie Azure do hostowania serwera aplikacji Java, takich jak Apache Tomcat.

Po zakończeniu pracy w tym przewodniku, należy zrozumieć sposób utworzyć maszynę wirtualną działającą na platformie Azure i skonfigurować je do uruchomienia serwera aplikacji Java. Zostanie Dowiedz się i wykonaj następujące zadania:

* Jak utworzyć maszyny wirtualnej, która ma Java Development Kit (JDK) już zainstalowane.
* Jak zdalnie logować się do maszyny wirtualnej.
* Jak zainstalować serwer aplikacji Java — Apache Tomcat — na maszynie wirtualnej.
* Jak utworzyć punkt końcowy dla maszyny wirtualnej.
* Jak otworzyć port w zaporze dla serwera aplikacji.

Ukończono instalację powoduje Tomcat uruchomione na maszynie wirtualnej.

![Maszyna wirtualna z systemem Apache Tomcat][virtual_machine_tomcat]

[!INCLUDE [create-account-and-vms-note](../../../../includes/create-account-and-vms-note.md)]

## <a name="to-create-a-virtual-machine"></a>Aby utworzyć maszynę wirtualną
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
2. Kliknij przycisk **Utwórz zasób**, kliknij przycisk **obliczeniowe**, następnie kliknij przycisk **zobaczyć wszystkie** w **wyróżnionych aplikacji**.
3. Kliknij przycisk **JDK**, kliknij przycisk **JDK 8** w **JDK** okienka.  
   Maszyny wirtualnej obrazy obsługujące **JDK 6** i **JDK 7** są dostępne, jeśli masz starsze aplikacje, które nie są gotowe do uruchomienia w JDK 8.
4. Wybierz w okienku JDK 8 **klasycznego**, następnie kliknij przycisk **Utwórz**.
5. W **podstawy** bloku:
   1. Określ nazwę maszyny wirtualnej.
   2. Wprowadź nazwę administratora **nazwy użytkownika** pola. Zapamiętaj nazwę tej i odpowiednie hasło w polu dalej. Należy je podczas zdalnego logowania się do maszyny wirtualnej.
   3. Wprowadź hasło w **nowe hasło** pola, a następnie wprowadź je ponownie w **Potwierdź hasło** pola. Jest to hasło dla konta administratora.
   4. Wybierz odpowiednie **subskrypcji**.
   5. Aby uzyskać **grupy zasobów**, kliknij przycisk **Utwórz nowy** , a następnie wprowadź nazwę nowej grupy zasobów. Lub kliknij przycisk **Użyj istniejącego** i wybierz jedną z dostępnych grup zasobów.
   6. Wybierz lokalizację, w której znajduje się maszyna wirtualna, takich jak **południowo-środkowe stany**.
6. Kliknij przycisk **Dalej**.
7. W **rozmiar obrazu maszyny wirtualnej** bloku, wybierz opcję **A1 standardowe** lub inny odpowiedni obraz.
8. Kliknij pozycję **Wybierz**.

9. W **ustawienia** bloku, kliknij przycisk **OK**. Można użyć wartości domyślnych przez platformę Azure.  
10. W **Podsumowanie** bloku, kliknij przycisk **OK**.

## <a name="to-remotely-sign-in-to-your-virtual-machine"></a>Zdalnie logować się do maszyny wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **maszyn wirtualnych (klasyczne)**. W razie potrzeby kliknij **więcej usług** w lewym dolnym rogu w kategoriach usługi. **Maszyn wirtualnych (klasyczne)** wpis ma na liście **obliczeniowe** grupy.
3. Kliknij nazwę maszyny wirtualnej, który chcesz zarejestrować się w usłudze.
4. Po uruchomieniu maszyny wirtualnej, menu w górnej części okienka zezwala na połączenia.
5. Kliknij przycisk **Połącz**.
6. Odpowiedz na monity w razie potrzeby można połączyć z maszyną wirtualną. Zwykle Zapisz lub Otwórz plik RDP, który zawiera informacje dotyczące połączenia. Może być konieczne skopiuj adres url: port jako ostatnia część pierwszy wiersz pliku RDP i wklej go w aplikacji zdalnej logowania.

## <a name="to-install-a-java-application-server-on-your-virtual-machine"></a>Aby zainstalować serwer aplikacji Java na maszynie wirtualnej
Serwer aplikacji Java można skopiować do maszyny wirtualnej, lub zainstalować serwer aplikacji Java za pomocą Instalatora.

Aby zainstalować w tym samouczku korzysta Tomcat jako serwera aplikacji Java.

1. Gdy użytkownik jest zalogowany z maszyną wirtualną, otwórz sesję przeglądarki na [Apache Tomcat](http://tomcat.apache.org/download-80.cgi).
2. Kliknij dwukrotnie łącze **Instalator usługi Windows 32-bitowe/64-bitowych**. Korzystając z tej techniki, Tomcat jest instalowany jako usługa systemu Windows.
3. Po wyświetleniu monitu uruchomić Instalatora.
4. W ramach **Apache Tomcat Instalator** kreatora, postępuj zgodnie z monitami dotyczącymi instalowania Tomcat. Do celów tego samouczka Zaakceptuj ustawienia domyślne funkcjonuje prawidłowo. Po przejściu **Kończenie pracy Kreatora instalacji Apache Tomcat** okno dialogowe, można opcjonalnie sprawdzić **Uruchom Apache Tomcat** ma teraz uruchomić Tomcat. Kliknij przycisk **Zakończ** do ukończenia procesu instalacji Tomcat.

## <a name="to-start-tomcat"></a>Aby uruchomić Tomcat

Otwieranie wiersza polecenia na maszynie wirtualnej, a następnie uruchamiając polecenie można uruchomić ręcznie Tomcat **net&nbsp;start&nbsp;Tomcat8**.

Po uruchomieniu Tomcat, możesz uzyskać dostęp Tomcat przy użyciu adresu URL <http://localhost:8080> w przeglądarce maszyny wirtualnej.

Aby wyświetlić Tomcat uruchomiona z zewnętrznych komputerów, należy utworzyć punktu końcowego i otworzyć port.

## <a name="to-create-an-endpoint-for-your-virtual-machine"></a>Aby utworzyć punkt końcowy dla maszyny wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Kliknij przycisk **maszyn wirtualnych (klasyczne)**.
3. Kliknij nazwę maszyny wirtualnej, na którym działa serwer aplikacji Java.
4. Kliknij przycisk **Punkty końcowe**.
5. Kliknij pozycję **Add** (Dodaj).
6. W **dodać punkt końcowy** okno dialogowe:
   1. Określ nazwę dla punktu końcowego; na przykład **HttpIn**.
   2. Wybierz **TCP** dla protokołu.
   3. Określ **80** portu publicznego.
   4. Określ **8080** dla port prywatny.
   5. Wybierz **wyłączone** dla pływający adres IP.
   6. Pozostaw listy kontroli dostępu, ponieważ jest.
   7. Kliknij przycisk **OK** przycisk, aby zamknąć okno dialogowe i utworzyć punktu końcowego.

## <a name="to-open-a-port-in-the-firewall-for-your-virtual-machine"></a>Aby otworzyć port w zaporze dla maszyny wirtualnej
1. Zaloguj się do maszyny wirtualnej.
2. Kliknij przycisk **Start systemu Windows**.
3. Kliknij przycisk **Panel sterowania**.
4. Kliknij przycisk **System i zabezpieczenia**, kliknij przycisk **zapory systemu Windows**, a następnie kliknij przycisk **Zaawansowane ustawienia**.
5. Kliknij przycisk **reguły ruchu przychodzącego**, a następnie kliknij przycisk **nową regułę**.  
   ![Nowej reguły ruchu przychodzącego][NewIBRule]
6. Dla **typ reguły**, wybierz pozycję **portu**, a następnie kliknij przycisk **dalej**.  
   ![Nowy port reguły dla ruchu przychodzącego][NewRulePort]
7. Na **protokoły i porty** ekranu wybierz **TCP**, określ **8080** jako **określonych portów lokalnych**, a następnie kliknij przycisk **dalej**.  
  ![Nowej reguły ruchu przychodzącego ][NewRuleProtocol]
8. Na **akcji** ekranu wybierz **zezwalały na połączenie**, a następnie kliknij przycisk **dalej**.
   ![Nowa akcja reguły dla ruchu przychodzącego][NewRuleAction]
9. Na **profilu** ekranu, upewnij się, że **domeny**, **prywatnej**, i **publicznego** są zaznaczone, a następnie kliknij przycisk **dalej**.
   ![Nowy profil reguły dla ruchu przychodzącego][NewRuleProfile]
10. Na **nazwa** ekranu, określ nazwę reguły, takie jak **HttpIn** (Nazwa reguły nie jest wymagany do dopasowania Nazwa punktu końcowego, jednak), a następnie kliknij przycisk **Zakończ**.  
    ![Nazwa nowej reguły ruchu przychodzącego][NewRuleName]

W tym momencie witryny sieci Web Tomcat powinien być widoczny z zewnętrznej przeglądarki. W oknie adresu w przeglądarce, wpisz adres URL w formacie **http://*Twojego\_DNS\_nazwa*. cloudapp.net**, gdzie ***Twojego\_DNS\_nazwa*** jest nazwą DNS, należy określić podczas tworzenia maszyny wirtualnej.

## <a name="application-lifecycle-considerations"></a>Zagadnienia dotyczące cyklu życia aplikacji
* Można tworzyć własne archiwum aplikacji sieci web (plik WAR) i dodaj go do **webapps** folderu. Na przykład Tworzenie podstawowego projektu sieci web dynamiczne strony usługi Java (JSP) i wyeksportować go jako plik WAR. Następnie skopiuj plik WAR do Apache Tomcat **webapps** folderu na maszynie wirtualnej, a następnie uruchom go w przeglądarce.
* Domyślnie po zainstalowaniu usługi Tomcat, jest ustawiona do uruchamiania ręcznego. Możesz przełączyć go do automatycznego uruchamiania przy użyciu przystawki usługi. Uruchom przystawkę usługi, klikając **Start systemu Windows**, **narzędzia administracyjne**, a następnie **usług**. Kliknij dwukrotnie **Apache Tomcat** usługi i ustaw **uruchamiana** do **automatyczne**.

    ![Ustawienie usługi do automatycznego uruchamiania][service_automatic_startup]

    O konieczności Tomcat automatycznego uruchamiania jest, że jej uruchomienie po ponownym uruchomieniu maszyny wirtualnej (na przykład po zainstalowaniu aktualizacji oprogramowania, które wymagają ponownego uruchomienia).

## <a name="next-steps"></a>Kolejne kroki
Informacje na temat innych usług (takich jak magazynu Azure, magistrali usług i bazy danych SQL), które warto uwzględnić z aplikacji Java. Wyświetlanie informacji dostępnych w [Centrum deweloperów języka Java](https://azure.microsoft.com/develop/java/).

[virtual_machine_tomcat]:media/java-run-tomcat-app-server/WA_VirtualMachineRunningApacheTomcat.png

[service_automatic_startup]:media/java-run-tomcat-app-server/WA_TomcatServiceAutomaticStart.png









[NewIBRule]:media/java-run-tomcat-app-server/NewInboundRule.png
[NewRulePort]:media/java-run-tomcat-app-server/NewRulePort.png
[NewRuleProtocol]:media/java-run-tomcat-app-server/NewRuleProtocol.png
[NewRuleAction]:media/java-run-tomcat-app-server/NewRuleAction.png
[NewRuleName]:media/java-run-tomcat-app-server/NewRuleName.png
[NewRuleProfile]:media/java-run-tomcat-app-server/NewRuleProfile.png


<!-- Deleted from the "To create an ednpoint for your virtual mache" 3/17/2017,
     to use the new portal.
6. In the **Add endpoint** dialog box, ensure **Add standalone endpoint** is selected, and then click **Next**.
7. In the **New endpoint details** dialog box:
-->
