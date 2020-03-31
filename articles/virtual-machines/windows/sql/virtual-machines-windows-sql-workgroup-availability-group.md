---
title: Konfigurowanie niezależnej od domeny grupy dyskowatej
description: Dowiedz się, jak skonfigurować grupę dyskowa dostępową niezależną od domeny usługi Active Directory Always On na maszynie wirtualnej programu SQL Server na platformie Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 72c04cf5e3e5fbdeac2d267dfc7b2703bd37a1c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122677"
---
# <a name="configure-a-workgroup-availability-group"></a>Konfigurowanie grupy dostępności grupy roboczej 

W tym artykule opisano kroki niezbędne do utworzenia klastra niezależnego od domeny usługi Active Directory z grupą dostępności zawsze włączone; jest to również nazywane klastrem grupy roboczej. W tym artykule skupiono się na krokach, które są istotne dla przygotowania i konfigurowania grupy roboczej i grupy dostępności, a także na temat kroków, które są opisane w innych artykułach, takich jak sposób tworzenia klastra lub wdrażania grupy dostępności. 


## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować grupę dostępności grupy roboczej, potrzebne są następujące elementy:
- Co najmniej dwie maszyny wirtualne systemu Windows Server 2016 (lub nowsze) z programem SQL Server 2016 (lub nowszym), wdrożone w tym samym zestawie dostępności lub w różnych strefach dostępności przy użyciu statycznych adresów IP. 
- Sieć lokalna z co najmniej 4 bezpłatnymi adresami IP w podsieci. 
- Konto na każdym komputerze w grupie administratorów, które ma również prawa do sysadmin w programie SQL Server. 
- Otwarte porty: TCP 1433, TCP 5022, TCP 59999. 

Dla odwołania w tym artykule są używane następujące parametry, ale można je zmodyfikować w razie potrzeby: 

| **Nazwa** | **Parametr** |
| :------ | :---------------------------------- |
| **Węzeł1**   | AGNode1 (10.0.0.4) |
| **Węzeł2**   | AGNode2 (10.0.0.5) |
| **Nazwa klastra** | AGWGAG (10.0.0.6) |
| **Odbiornika** | AGListener (10.0.0.7) | 
| **Sufiks DNS** | ag.wgcluster.example.com | 
| **Nazwa grupy roboczej** | Grupa AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-dns-suffix"></a>Ustawianie sufiksu DNS 

W tym kroku skonfiguruj sufiks DNS dla obu serwerów. Na przykład `ag.wgcluster.example.com`. Dzięki temu można użyć nazwy obiektu, z którego chcesz się połączyć, jako `AGNode1.ag.wgcluster.example.com`w pełni kwalifikowanego adresu w sieci, takiego jak . 

Aby skonfigurować sufiks DNS, wykonaj następujące czynności:

1. RDP do pierwszego węzła i otwórz Menedżera serwera. 
1. Wybierz **pozycję Serwer lokalny,** a następnie wybierz nazwę maszyny wirtualnej w obszarze **Nazwa komputera**. 
1. Wybierz **pozycję Zmień...** w obszarze **Aby zmienić nazwę tego komputera...**. 
1. Zmień nazwę nazwy grupy roboczej na coś `AGWORKGROUP`znaczącego, na przykład: 

   ![Zmienianie nazwy grupy roboczej](media/virtual-machines-windows-sql-workgroup-availability-group/1-change-workgroup-name.png)

1. Wybierz **więcej...,** aby otworzyć okno dialogowe **Sufiks DNS i Nazwa komputera NetBIOS.** 
1. Wpisz nazwę sufiksu DNS w obszarze **Podstawowy sufiks DNS tego komputera,** na `ag.wgcluster.example.com` przykład, a następnie wybierz przycisk **OK:** 

   ![Dodawanie sufiksu DNS](media/virtual-machines-windows-sql-workgroup-availability-group/2-add-dns-suffix.png)

1. Upewnij się, że **pełna nazwa komputera** wyświetla teraz sufiks DNS, a następnie wybierz przycisk **OK,** aby zapisać zmiany: 

   ![Dodawanie sufiksu DNS](media/virtual-machines-windows-sql-workgroup-availability-group/3-confirm-full-computer-name.png)

1. Uruchom ponownie serwer, gdy zostanie wyświetlony monit, aby to zrobić. 
1. Powtórz te kroki w innych węzłach, które mają być używane dla grupy dostępności. 

## <a name="edit-host-file"></a>Edytowanie pliku hosta

Ponieważ nie ma usługi active directory, nie ma możliwości uwierzytelniania połączeń z systemem Windows. W związku z tym należy przypisać zaufanie, edytując plik hosta za pomocą edytora tekstu. 

Aby edytować plik hosta, wykonaj następujące czynności:

1. RDP do maszyny wirtualnej. 
1. Użyj **Eksploratora plików,** aby przejść do `c:\windows\system32\drivers\etc`pliku . 
1. Kliknij prawym przyciskiem myszy plik **hosts** i otwórz plik za pomocą **Notatnika** (lub dowolnego innego edytora tekstu).
1. Na końcu pliku dodaj wpis dla każdego węzła, grupy dostępności i odbiornika `IP Address, DNS Suffix #comment` w postaci podobnych: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Dodawanie wpisów adresu IP, klastra i odbiornika do pliku hosta](media/virtual-machines-windows-sql-workgroup-availability-group/4-host-file.png)

## <a name="set-permissions"></a>Ustawianie uprawnień

Ponieważ nie ma usługi Active Directory do zarządzania uprawnieniami, należy ręcznie zezwolić na utworzenie klastra niezabudowane konto administratora lokalnego. 

Aby to zrobić, uruchom następujące polecenie cmdlet programu PowerShell w administracyjnej sesji programu PowerShell w każdym węźle: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Tworzenie klastra trybu failover

W tym kroku utworzysz klaster trybu failover. Jeśli nie znasz tych kroków, możesz wykonać je z [samouczka klastra trybu failover](virtual-machines-windows-portal-sql-create-failover-cluster.md#step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct).

Znaczące różnice między samouczka i co należy zrobić dla klastra grupy roboczej:
- Podczas **uruchamiania**sprawdzania poprawności klastra wyeszła zaznaczyć zaznaczenie opcji Magazyn i **Bezpośrednie miejsca do magazynowania.** 
- Podczas dodawania węzłów do klastra dodaj w pełni kwalifikowaną nazwę, taką jak:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Wyeszła **zaznaczyć pozycję Dodaj wszystkie kwalifikujące się miejsca do magazynu do klastra**. 

Po utworzeniu klastra przypisz statyczny adres IP klastra. Aby to zrobić, wykonaj następujące kroki:

1. W jednym z węzłów otwórz **Menedżera klastrów trybu failover**, wybierz klaster, kliknij prawym przyciskiem myszy **pozycję Nazwa: \<Nazwa klastra>** w obszarze Podstawowe zasoby **klastra,** a następnie wybierz polecenie **Właściwości**. 

   ![Właściwości uruchamiania nazwy klastra](media/virtual-machines-windows-sql-workgroup-availability-group/5-launch-cluster-name-properties.png)

1. Wybierz adres IP w obszarze **Adresy IP** i wybierz pozycję **Edytuj**. 
1. Wybierz **opcję Użyj statycznego**, podaj adres IP klastra, a następnie wybierz przycisk **OK:** 

   ![Podaj statyczny adres IP dla klastra](media/virtual-machines-windows-sql-workgroup-availability-group/6-provide-static-ip-for-cluster.png)

1. Sprawdź, czy ustawienia wyglądają poprawnie, a następnie wybierz **przycisk OK,** aby je zapisać:

   ![Weryfikowanie właściwości klastra](media/virtual-machines-windows-sql-workgroup-availability-group/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Tworzenie monitora w chmurze 

W tym kroku skonfiguruj monitor udostępniania w chmurze. Jeśli nie znasz tych kroków, zobacz [samouczek klastra trybu failover](virtual-machines-windows-portal-sql-create-failover-cluster.md#create-a-cloud-witness). 

## <a name="enable-availability-group-feature"></a>Włącz funkcję grupy dostępności 

W tym kroku włącz funkcję grupy dostępności. Jeśli nie znasz tych kroków, zapoznaj się z [samouczkiem grupy dostępności](virtual-machines-windows-portal-sql-availability-group-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificate"></a>Tworzenie kluczy i certyfikatów

W tym kroku utwórz certyfikaty używane przez logowanie SQL w zaszyfrowanym punkcie końcowym. Utwórz folder w każdym węźle, aby `c:\certs`przechowywać kopie zapasowe certyfikatów, takie jak . 

Aby skonfigurować pierwszy węzeł, wykonaj następujące kroki: 

1. Otwórz **program SQL Server Management Studio** i `AGNode1`połącz się z pierwszym węzłem, takim jak . 
1. Otwórz okno **Nowe zapytanie** i uruchom następującą instrukcję Transact-SQL (T-SQL) po zaktualizowaniu do złożonego i bezpiecznego hasła:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. Następnie utwórz punkt końcowy HADR i użyj certyfikatu do uwierzytelniania, uruchamiając tę instrukcję Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Użyj **Eksploratora plików,** aby przejść do `c:\certs`lokalizacji pliku, w której znajduje się certyfikat, na przykład . 
1. Ręcznie zrób kopię certyfikatu, na `AGNode1Cert.crt`przykład , z pierwszego węzła i przenieś go do tej samej lokalizacji w drugim węźle. 

Aby skonfigurować drugi węzeł, wykonaj następujące czynności: 

1. Połącz się z drugim węzłem za `AGNode2`pomocą programu SQL Server Management **Studio**, na przykład . 
1. W oknie **Nowe zapytanie** uruchom następującą instrukcję Transact-SQL (T-SQL) po zaktualizowaniu do złożonego i bezpiecznego hasła: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. Następnie utwórz punkt końcowy HADR i użyj certyfikatu do uwierzytelniania, uruchamiając tę instrukcję Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Użyj **Eksploratora plików,** aby przejść do `c:\certs`lokalizacji pliku, w której znajduje się certyfikat, na przykład . 
1. Ręcznie zrób kopię certyfikatu, na `AGNode2Cert.crt`przykład , z drugiego węzła i przenieś go do tej samej lokalizacji w pierwszym węźle. 

Jeśli w klastrze znajdują się inne węzły, powtórz te kroki, modyfikując odpowiednie nazwy certyfikatów. 

## <a name="create-logins"></a>Tworzenie loginów

Uwierzytelnianie certyfikatu służy do synchronizowania danych między węzłami. Aby na to zezwolić, utwórz login dla innego węzła, utwórz użytkownika dla logowania, utwórz certyfikat dla logowania, aby użyć kopii zapasowej certyfikatu, a następnie udzielić połączenia w dublowaniu punktu końcowego. 

Aby to zrobić, najpierw uruchom następującą kwerendę Transact-SQL (T-SQL) w pierwszym węźle, na `AGNode1`przykład: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

Następnie uruchom następującą kwerendę Transact-SQL (T-SQL) w `AGNode2`drugim węźle, na przykład: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Jeśli w klastrze znajdują się inne węzły, powtórz te kroki, modyfikując odpowiednie nazwy certyfikatów i użytkowników. 

## <a name="configure-availability-group"></a>Konfigurowanie grupy dostępności

W tym kroku skonfiguruj grupę dostępności i dodaj do niej bazy danych. W tej chwili nie należy tworzyć odbiornika. Jeśli nie znasz tych kroków, zobacz [samouczek grupy dostępności](virtual-machines-windows-portal-sql-availability-group-tutorial.md#create-the-availability-group). Pamiętaj, aby zainicjować pracę po awarii i powrót po awarii, aby sprawdzić, czy wszystko działa tak, jak powinno być. 

   > [!NOTE]
   > Jeśli wystąpi błąd podczas procesu synchronizacji, może `NT AUTHORITY\SYSTEM` być konieczne przyznanie praw sysadmin do tworzenia `AGNode1` zasobów klastra w pierwszym węźle, takich jak tymczasowo. 

## <a name="configure-load-balancer"></a>Konfigurowanie modułu równoważenia obciążenia

W tym ostatnim kroku skonfiguruj moduł równoważenia obciążenia przy użyciu [portalu Azure lub](virtual-machines-windows-portal-sql-alwayson-int-listener.md) programu [PowerShell](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md)


## <a name="next-steps"></a>Następne kroki

Można również użyć [interfejsu wiersza polecenia Az SQL VM CLI,](virtual-machines-windows-sql-availability-group-cli.md) aby skonfigurować grupę dostępności. 


