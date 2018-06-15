---
title: Konfigurowanie Apache Tomcat na maszynie wirtualnej systemu Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować Apache Tomcat7 przy użyciu maszyn wirtualnych Azure z systemem Linux.
services: virtual-machines-linux
documentationcenter: ''
author: NingKuang
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 45ecc89c-1cb0-4e80-8944-bd0d0bbedfdc
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/15/2015
ms.author: ningk
ms.openlocfilehash: 161a56a019f8c2c8ce5e3890e73ad5c5710e7b82
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841619"
---
# <a name="set-up-tomcat7-on-a-linux-virtual-machine-with-azure"></a>Konfigurowanie Tomcat7 na maszynie wirtualnej systemu Linux przy użyciu platformy Azure
Apache Tomcat (lub po prostu Tomcat, również nazywanych Jakarta Tomcat) to serwer sieci web typu open source i kontener serwlet opracowane przez Foundation oprogramowania Apache (ASF). Tomcat implementuje Serwlet Java i specyfikacje JavaServer Pages (JSP) z Sun Microsystems. Tomcat zapewnia czysty Java HTTP środowisku serwera sieci web do uruchamiania kodu języka Java. W najprostszej konfiguracji Tomcat działa w procesie jeden system operacyjny. Ten proces jest uruchomiony maszyny wirtualnej Java (JVM). Każde żądanie HTTP z przeglądarki do serwera Tomcat jest przetwarzany jako oddzielnym wątku w procesie Tomcat.  

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [usługi Azure Resource Manager i Model Klasyczny](../../../resource-manager-deployment-model.md). W tym artykule opisano sposób użycia klasycznym modelu wdrażania. Zaleca się, że większości nowych wdrożeń Użyj modelu Resource Manager. Aby użyć szablonu usługi Resource Manager do wdrażania maszyny Wirtualnej systemu Ubuntu JDK otwarte i Tomcat, zobacz [w tym artykule](https://azure.microsoft.com/documentation/templates/openjdk-tomcat-ubuntu-vm/).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

W tym artykule zainstalujesz Tomcat7 w obrazie systemu Linux i wdrożyć go na platformie Azure.  

Dowiesz się:  

* Jak utworzyć maszynę wirtualną na platformie Azure.
* Jak przygotować maszyny wirtualnej do Tomcat7.
* Jak zainstalować Tomcat7.

Zakłada się, że masz już subskrypcję platformy Azure.  Jeśli nie, można założyć bezpłatnej wersji próbnej na [witrynie systemu Azure](https://azure.microsoft.com/). Jeśli masz subskrypcję MSDN, zobacz [specjalne cennik Microsoft Azure: MSDN, MPN i korzyści BizSpark](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/?c=14-39). Aby dowiedzieć się więcej na temat usługi Azure, zobacz [co to jest Azure?](https://azure.microsoft.com/overview/what-is-azure/).

W tym artykule przyjęto założenie, że masz podstawową wiedzę na temat pracy z Tomcat i Linux.  

## <a name="phase-1-create-an-image"></a>Faza 1: Tworzenie obrazu
W tej fazie spowoduje utworzenie maszyny wirtualnej za pomocą obrazu systemu Linux na platformie Azure.  

### <a name="step-1-generate-an-ssh-authentication-key"></a>Krok 1: Generowanie klucza uwierzytelniania SSH
SSH jest narzędziem ważne dla administratorów systemu. Konfigurowanie zabezpieczeń dostępu na podstawie hasła ustalić ludzkich nie jest jednak zalecane. Złośliwi użytkownicy można podzielić na podstawie nazwy użytkownika i hasło słabe systemu.

Dobre wieści jest brak sposób pozostaw otwarte dostępu zdalnego i nie martw się o hasłach. Ta metoda składa się z uwierzytelniania za pomocą kryptografii asymetrycznej. Klucz prywatny użytkownika jest przyznająca uwierzytelniania. Konto użytkownika nie zezwalać na uwierzytelnianie hasła nawet można zablokować.

Zaletą tej metody jest niepotrzebne różne hasła do logowania się na różnych serwerach. Można uwierzytelniać za pomocą klucza prywatnego osobistego na wszystkich serwerach, co zapobiega trzeba pamiętać kilka haseł.



Wykonaj następujące kroki, aby wygenerować klucz uwierzytelniania SSH.

1. Pobierz i zainstaluj PuTTYgen z następującej lokalizacji: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2. Uruchom Puttygen.exe.
3. Kliknij przycisk **Generuj** do generowania kluczy. W procesie można zwiększyć losowości przez przesuwanie wskaźnika myszy nad obszarem puste okno.  
   ![PuTTY zrzut ekranu generatora klucza, pokazujący przycisk Generuj nowego klucza][1]
4. Po procesie generowania Puttygen.exe wyświetli nowy klucz publiczny.  
   ![PuTTY przedstawia nowego klucza publicznego i Zapisz zrzut ekranu w generatora klucza prywatnego klucza przycisku][2]
5. Zaznacz i skopiuj klucz publiczny i zapisz go w pliku o nazwie publicKey.pem. Nie klikaj pozycji **Zapisz klucz publiczny**, ponieważ format pliku zapisanego klucza publicznego różni się od chcemy klucza publicznego.
6. Kliknij przycisk **Zapisz klucz prywatny**i zapisz go w pliku o nazwie privateKey.ppk.

### <a name="step-2-create-the-image-in-the-azure-portal"></a>Krok 2: Tworzenie obrazu w portalu Azure
1. W [portal](https://portal.azure.com/), kliknij przycisk **Utwórz zasób** na pasku zadań do utworzenia obrazu. Następnie wybierz obraz systemu Linux, który jest na podstawie Twoich potrzeb. W poniższym przykładzie użyto Ubuntu 14.04 obrazu.
![Zrzut ekranu pokazujący przycisk Nowy portalu][3]

2. Aby uzyskać **nazwy hosta**, określ nazwę dla adresu URL, który możesz i klientów internetowych uzyskują dostęp do tej maszyny wirtualnej. Zdefiniuj ostatnia część nazwy DNS, na przykład tomcatdemo. Azure wygeneruje wtedy adres URL jako tomcatdemo.cloudapp.net.  

3. Dla **klucz uwierzytelniania SSH**, skopiuj wartość klucza z pliku publicKey.pem, który zawiera klucz publiczny, generowane przez PuTTYgen.  
![Pole klucza uwierzytelniania SSH w portalu][4]

4. Skonfiguruj inne ustawienia, a następnie kliknij przycisk **Utwórz**.  

## <a name="phase-2-prepare-your-virtual-machine-for-tomcat7"></a>Faza 2: Przygotowanie maszyny wirtualnej do Tomcat7
W tym kroku zostanie konfigurowania punktu końcowego dla ruchu Tomcat, a następnie podłącz do nowej maszyny wirtualnej.

### <a name="step-1-open-the-http-port-to-allow-web-access"></a>Krok 1: Otwórz port HTTP, aby umożliwić dostęp w sieci web
Punkty końcowe platformy Azure składają się z protokołem TCP lub UDP, oraz port publiczny i prywatny. Port prywatny jest port, który usługa nasłuchuje na maszynie wirtualnej. Port publiczny jest port nasłuchujący usługi w chmurze Azure zewnętrznie dla ruchu przychodzącego, internetowego.  

TCP port 8080 jest domyślny numer portu serwera Tomcat używa do nasłuchiwania. Ten port jest otwarty z punktu końcowego platformy Azure, możesz i innych Klienci internetowi mogą uzyskiwać dostęp stron Tomcat.  

1. W portalu kliknij **Przeglądaj** > **maszyn wirtualnych**, a następnie kliknij maszynę wirtualną, która została utworzona.  
   ![Zrzut ekranu przedstawiający katalogu maszyny wirtualne][5]
2. Aby dodać punkt końcowy maszyny wirtualnej, kliknij przycisk **punkty końcowe** pole.
   ![Zrzut ekranu pokazujący pole punkty końcowe][6]
3. Kliknij pozycję **Add** (Dodaj).  

   1. Dla punktu końcowego, wprowadź nazwę punktu końcowego w **punktu końcowego**, a następnie wprowadź 80 w **Port publiczny**.  

      Jeśli zostanie ustawiona do 80, nie trzeba Podaj numer portu w adresie URL, który umożliwia dostęp do serwera Tomcat. Na przykład http://tomcatdemo.cloudapp.net.    

      Jeśli zostanie ustawiona na inną wartość, takich jak 81, musisz dodać numer portu do adresu URL do serwera Tomcat. Przykład: http://tomcatdemo.cloudapp.net:81/.
   2. Wprowadź 8080 w **Port prywatny**. Domyślnie Tomcat nasłuchuje na porcie TCP 8080. Jeśli zmieniono domyślne nasłuchiwania port serwera Tomcat, należy zaktualizować **Port prywatny** być taka sama jak Tomcat nasłuchiwać portu.  
      ![Zrzut ekranu z interfejsu użytkownika, który zawiera polecenie Dodaj, Port publiczny i Port prywatny][7]
4. Kliknij przycisk **OK** można dodać punktu końcowego do maszyny wirtualnej.

### <a name="step-2-connect-to-the-image-you-created"></a>Krok 2: Łączenie z obrazu, który został utworzony
Można wybrać dowolnego narzędzia SSH, aby połączyć się z maszyną wirtualną. W tym przykładzie używamy programu PuTTY.  

1. Pobierz nazwę DNS maszyny wirtualnej z portalu.
    1. Kliknij przycisk **Przeglądaj** > **maszyn wirtualnych**.
    2. Wybierz nazwę maszyny wirtualnej, a następnie kliknij przycisk **właściwości**.
    3. W **właściwości** kafelka, Szukaj w **nazwy domeny** pole, aby pobrać nazwę DNS.  

2. Pobierz numer portu dla połączeń SSH z **SSH** pole.  
![Zrzut ekranu pokazujący numer portu połączenia SSH][8]

3. Pobierz [PuTTY](http://www.putty.org/).  

4. Po pobraniu, kliknij plik wykonywalny Putty.exe. W konfiguracji programu PuTTY skonfiguruj podstawowe opcje przy użyciu nazwy hosta i portu numer uzyskany z właściwości maszyny wirtualnej.   
![Zrzut ekranu pokazujący opcji nazwę i port hosta Konfiguracja programu PuTTY][9]

5. W okienku po lewej stronie kliknij **połączenia** > **SSH** > **uwierzytelniania**, a następnie kliknij przycisk **Przeglądaj** Określ lokalizację pliku privateKey.ppk. Plik privateKey.ppk zawiera klucz prywatny, który jest generowany przez PuTTYgen wcześniej w "faza 1: Tworzenie obrazu" w dalszej części tego artykułu.  
![Zrzut ekranu pokazujący hierarchii katalogów połączenia i przycisk przeglądania][10]

6. Kliknij przycisk **Otwórz**. Użytkownik może otrzymywać alerty przez okno komunikatu. Jeśli skonfigurowano nazwę DNS i numer portu poprawnie, kliknij przycisk **tak**.
![Zrzut ekranu pokazujący powiadomienia.][11]

7. Monit o wprowadzenie nazwy użytkownika.  
![Zrzut ekranu pokazujący, gdzie można wprowadzić nazwę użytkownika][12]

8. Wprowadź nazwę użytkownika, który został użyty do utworzenia maszyny wirtualnej w "faza 1: Tworzenie obrazu" wcześniej w tym artykule. Zostanie wyświetlony ekran podobny do następującego:  
![Zrzut ekranu pokazujący potwierdzenie uwierzytelniania][13]

## <a name="phase-3-install-software"></a>Faza 3: Instalacja oprogramowania
Na tym etapie należy zainstalować środowisko uruchomieniowe języka Java, Tomcat7 i inne składniki Tomcat7.  

### <a name="java-runtime-environment"></a>Środowisko uruchomieniowe języka Java
Tomcat jest napisany w języku Java. Istnieją dwa rodzaje Java Development Kit (JDKs), OpenJDK i Oracle JDK. Można wybrać jednego z nich.  

> [!NOTE]
> Zarówno JDKs mają niemal taki sam kod do klasy w interfejsie API języka Java, ale jest różny kod dla maszyny wirtualnej. Zwykle OpenJDK korzystać otwarte biblioteki, w trakcie zwykle Oracle JDK zamknięte z nich korzystać. Oracle JDK ma więcej klasy, a niektóre stałe usterek i Oracle JDK jest stabilna więcej niż OpenJDK.

#### <a name="install-openjdk"></a>Zainstaluj OpenJDK  

Użyj następującego polecenia, aby pobrać OpenJDK.   

    sudo apt-get update  
    sudo apt-get install openjdk-7-jre  


* Aby utworzyć katalog zawierający pliki JDK:  

        sudo mkdir /usr/lib/jvm  
* Aby wyodrębnić pliki JDK w katalogu/usr/lib/jvm /:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/

#### <a name="install-oracle-jdk"></a>Zainstaluj Oracle JDK


Użyj następującego polecenia, aby pobrać z witryny sieci Web programu Oracle Oracle JDK.  

     wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u5-b13/jdk-8u5-linux-x64.tar.gz  
* Aby utworzyć katalog zawierający pliki JDK:  

        sudo mkdir /usr/lib/jvm  
* Aby wyodrębnić pliki JDK w katalogu/usr/lib/jvm /:  

        sudo tar -zxf jdk-8u5-linux-x64.tar.gz  -C /usr/lib/jvm/  
* Aby ustawić Oracle JDK jako maszynę wirtualną Java domyślne:  

        sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk1.8.0_05/bin/java 100  

        sudo update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk1.8.0_05/bin/javac 100  

#### <a name="confirm-that-java-installation-is-successful"></a>Upewnij się, że Java Instalacja powiodła się
Polecenie podobnie do następującej umożliwia sprawdzić, czy środowiska uruchomieniowego jest poprawnie zainstalowany:  

    java -version  

Jeśli zainstalowano OpenJDK, powinien zostać wyświetlony komunikat podobnie do następującej: ![OpenJDK pomyślnej instalacji wiadomości][14]

Jeśli zainstalowano Oracle JDK, powinien zostać wyświetlony komunikat podobnie do następującej: ![komunikat instalacji pomyślne JDK Oracle][15]

### <a name="install-tomcat7"></a>Zainstaluj Tomcat7
Użyj następującego polecenia, aby zainstalować Tomcat7.  

    sudo apt-get install tomcat7  

Jeśli nie używasz Tomcat7, użyj odpowiednią odmianę tego polecenia.  

#### <a name="confirm-that-tomcat7-installation-is-successful"></a>Upewnij się, że Tomcat7 Instalacja powiodła się
Aby sprawdzić, czy pomyślnie zainstalowano Tomcat7, przejdź do nazwy DNS serwera Tomcat. W tym artykule jest przykładowy adres URL http://tomcatexample.cloudapp.net/. Jeśli zostanie wyświetlony komunikat z podobnie do następującej Tomcat7 jest poprawnie zainstalowany.
![Komunikat instalacji Tomcat7 powiodło się][16]

### <a name="install-other-tomcat7-components"></a>Instalowanie innych składników Tomcat7
Istnieją inne składniki opcjonalne Tomcat, które można zainstalować.  

Użyj **tomcat7 stanie pamięci podręcznej wyszukiwania sudo** polecenie, aby wyświetlić wszystkie dostępne składniki. Użyj następujących poleceń, aby zainstalować niektórych składników przydatne.  

    sudo apt-get install tomcat7-admin      #admin web applications

    sudo apt-get install tomcat7-user         #tools to create user instances  

## <a name="phase-4-configure-tomcat7"></a>Faza 4: Konfigurowanie Tomcat7
Na tym etapie można administrować Tomcat.

### <a name="start-and-stop-tomcat7"></a>Uruchamianie i zatrzymywanie Tomcat7
Serwer Tomcat7 rozpocznie się automatycznie po zainstalowaniu go. Możesz również uruchomić go za pomocą następującego polecenia:   

    sudo /etc/init.d/tomcat7 start

Aby zatrzymać Tomcat7:

    sudo /etc/init.d/tomcat7 stop

Aby wyświetlić stan Tomcat7:

    sudo /etc/init.d/tomcat7 status

Aby ponownie uruchomić usługi Tomcat: 

    sudo /etc/init.d/tomcat7 restart

### <a name="tomcat7-administration"></a>Administracja Tomcat7
Można edytować plik konfiguracji użytkownika Tomcat, aby skonfigurować swoje poświadczenia administratora. Użyj następującego polecenia:  

    sudo vi  /etc/tomcat7/tomcat-users.xml   

Oto przykład:  
![Zrzut ekranu pokazujący danych wyjściowych polecenia sudo vi][17]  

> [!NOTE]
> Utwórz silne hasło dla nazwy użytkownika administratora.  

Po zmodyfikowaniu tego pliku, należy ponownie uruchomić usługi Tomcat7 przy użyciu następującego polecenia, aby upewnić się, że zmiany wprowadzone:  

    sudo /etc/init.d/tomcat7 restart  

Otwórz przeglądarkę i wprowadź **http://<your tomcat server DNS name>/Menedżera/html** jako adresu URL. Na przykład w tym artykule, adres URL jest http://tomcatexample.cloudapp.net/manager/html.  

Po połączeniu, powinny zostać wyświetlone informacje podobne do następujących:  
![Zrzut ekranu Menedżera aplikacji sieci Web Tomcat][18]

## <a name="common-issues"></a>Typowe problemy
### <a name="cant-access-the-virtual-machine-with-tomcat-and-moodle-from-the-internet"></a>Nie ma dostępu do maszyny wirtualnej z serwera Tomcat i Moodle z Internetu
#### <a name="symptom"></a>Objaw  
  Tomcat jest uruchomiony, ale nie widać Tomcat domyślną stronę w przeglądarce.
#### <a name="possible-root-cause"></a>Możliwe przyczyny   

  * Port nasłuchiwania Tomcat nie jest taki sam jak port prywatny punktu końcowego maszyny wirtualnej dla ruchu Tomcat.  

     Sprawdź ustawienia punktu końcowego port prywatny i port publiczny i upewnij się, że port prywatny jest taka sama jak Tomcat nasłuchiwać portu. Zobacz "faza 1: Tworzenie obrazu" tego artykułu, aby uzyskać instrukcje dotyczące konfigurowania punktów końcowych dla maszyny wirtualnej.  

     Aby określić port nasłuchiwania Tomcat, otwórz /etc/httpd/conf/httpd.conf (Red Hat release) lub /etc/tomcat7/server.xml (wersja Debian). Domyślnie port nasłuchiwania Tomcat jest 8080. Oto przykład:  

        <Connector port="8080" protocol="HTTP/1.1"  connectionTimeout="20000"   URIEncoding="UTF-8"            redirectPort="8443" />  

     Jeśli używasz maszynę wirtualną, takie jak Debian i Ubuntu i chcesz zmienić domyślny port z Tomcat nasłuchiwania (na przykład 8081), należy również otworzyć port dla systemu operacyjnego. Najpierw należy otworzyć profilu:  

        sudo vi /etc/default/tomcat7  

     Następnie usuń komentarz ostatniego wiersza i zmień "nie" na "tak".  

        AUTHBIND=yes
  2. Zapora wyłączył nasłuchiwania port serwera Tomcat.

     Może zobaczyć tylko Tomcat domyślną stronę z hosta lokalnego. Problem jest najbardziej prawdopodobne, że portu, który jest nasłuch przez Tomcat jest blokowany przez zaporę. Można użyć narzędzia w3m, aby przejść do strony sieci Web. Następujące polecenia Zainstaluj w3m i przejdź do domyślnej strony Tomcat:  


        sudo yum instalacji w3m w3m-img


        w3m http://localhost:8080  
#### <a name="solution"></a>Rozwiązanie

  * Jeśli Tomcat nasłuchiwać portu nie jest taki sam jak port prywatny punktu końcowego dla ruchu do maszyny wirtualnej, należy zmienić port prywatny jest taka sama jak Tomcat nasłuchiwać portu.   
  2. Jeśli Zapora/iptables przyczyną problemu, należy dodać następujące wiersze do /etc/sysconfig/iptables. Drugi wiersz jest potrzebna tylko dla ruchu https:  

      -A -p tcp -m tcp--dport 80 -j AKCEPTUJ wejściowych

      -A -p tcp -m tcp--dport 443 -j AKCEPTUJ wejściowych  

     > [!IMPORTANT]
     > Upewnij się, że wierszy poprzedniej będą umieszczane powyżej, które globalnie ograniczałyby dostępu, takich jak następujące: - wejściowych -j Odrzuć — Odrzuć with zabronione hostów protokołu icmp



Aby ponownie załadować iptables, uruchom następujące polecenie:

    service iptables restart

To została przetestowana CentOS 6.3.

### <a name="permission-denied-when-you-upload-project-files-to-varlibtomcat7webapps"></a>Pozwolono podczas przekazywania plików projektu aby /var/lib/tomcat7/webapps /
#### <a name="symptom"></a>Objaw
  Korzystając z klienta protokołu SFTP (na przykład FileZilla) do łączenia się z maszyną wirtualną i przejdź do /var/lib/tomcat7/webapps/opublikowanie witryny, zostanie wyświetlony komunikat o błędzie podobny do następującego:  

     status:    Listing directory /var/lib/tomcat7/webapps
     Command:    put "C:\Users\liang\Desktop\info.jsp" "info.jsp"
     Error:    /var/lib/tomcat7/webapps/info.jsp: open for write: permission denied
     Error:    File transfer failed
#### <a name="possible-root-cause"></a>Możliwe przyczyny
  Nie masz uprawnień dostępu do folderu /var/lib/tomcat7/webapps.  
#### <a name="solution"></a>Rozwiązanie  
  Należy uzyskać uprawnienia z konta głównego. Własności tego folderu z głównego można zmienić nazwy użytkownika używane podczas przydzielania maszyny. Oto przykład nazwą konta azureuser:  

     sudo chown azureuser -R /var/lib/tomcat7/webapps

  Użyj opcji -R zbyt zastosować uprawnienia dla wszystkich plików w katalogu.  

  To polecenie działa również w przypadku katalogów. Opcja -R zmienia uprawnienia dla wszystkich plików i katalogów w katalogu. Oto przykład:  

     sudo chown -R username:group directory  

  To polecenie powoduje zmianę własności (użytkowników i grup) w przypadku wszystkich plików i katalogów, które znajdują się w katalogu.  

  Polecenie zmienia tylko uprawnienia katalogu folderu. Pliki i foldery w katalogu nie są zmieniane.  

     sudo chown username:group directory

[1]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-01.png
[2]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-02.png
[3]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-03.png
[4]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-04.png
[5]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-05.png
[6]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-06.png
[7]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-07.png
[8]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-08.png
[9]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-09.png
[10]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-10.png
[11]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-11.png
[12]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-12.png
[13]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-13.png
[14]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-14.png
[15]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-15.png
[16]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-16.png
[17]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-17.png
[18]:media/setup-tomcat/virtual-machines-linux-setup-tomcat7-linux-18.png
