---
title: Szyfrowanie maszyny wirtualnej platformy Azure | Microsoft Docs
description: Ten dokument zawiera informacje ułatwiające szyfrowanie maszyny wirtualnej platformy Azure po odebraniu alertu z Centrum zabezpieczeń Azure.
services: security, security-center
documentationcenter: na
author: TomShinder
manager: swadhwa
editor: ''
ms.assetid: f6c28bc4-1f79-4352-89d0-03659b2fa2f5
ms.service: security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomsh
ms.openlocfilehash: 9a376eb63e7ba054a125666f95c05d5e7dfb5470
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="encrypt-an-azure-virtual-machine"></a>Szyfrowanie maszyny wirtualnej platformy Azure
Jeśli masz maszyny wirtualne, które nie są szyfrowane, w Centrum zabezpieczeń Azure zostanie wyświetlony alert. Te alerty będą widoczne jako alerty o wysokiej ważności. Zaleca się zaszyfrowanie tych maszyn wirtualnych.

![Zalecenia dotyczące szyfrowania dysków](./media/security-center-disk-encryption/security-center-disk-encryption-fig1.png)

> [!NOTE]
> Informacje przedstawione w tym dokumencie dotyczą szyfrowania maszyn wirtualnych bez użycia klucza szyfrowania (który jest wymagany do tworzenia kopii zapasowych maszyn wirtualnych za pomocą usługi Azure Backup). Informacje na temat sposobu użycia klucza szyfrowania do obsługi usługi Azure Backup dla zaszyfrowanych maszyn wirtualnych platformy Azure można znaleźć w artykule [Azure Disk Encryption for Windows and Linux Azure Virtual Machines](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) (Szyfrowanie dysków Azure dla maszyn wirtualnych platformy Azure z systemem Windows i Linux).
>
>

Aby zaszyfrować maszyny wirtualne platformy Azure, które zostały zidentyfikowane przez Centrum zabezpieczeń Azure jako wymagające szyfrowania, zaleca się wykonanie następujących czynności:

* Zainstaluj i skonfiguruj program Azure PowerShell. Umożliwi to uruchomienie poleceń programu PowerShell wymaganych do skonfigurowania wymagań wstępnych dotyczących szyfrowania maszyn wirtualnych platformy Azure.
* Uzyskaj i uruchom skrypt wymagań wstępnych szyfrowania dysków Azure w programie Azure PowerShell.
* Zaszyfruj maszyny wirtualne.

Celem tego dokumentu jest umożliwienie zaszyfrowania maszyn wirtualnych także użytkownikom, którzy nie znają programu Azure PowerShell lub znają go słabo.
W tym dokumencie przyjęto założenie, że jako komputer kliencki jest używany komputer z systemem Windows 10, na którym będzie konfigurowane szyfrowanie dysków Azure.

Istnieje wiele metod konfigurowania wymagań wstępnych i konfigurowania szyfrowania maszyn wirtualnych platformy Azure. Jeśli użytkownik ma dużą wiedzę na temat programu Azure PowerShell lub interfejsu wiersza polecenia Azure, może wybrać inne rozwiązania.

> [!NOTE]
> Aby dowiedzieć się więcej na temat innych sposobów konfigurowania szyfrowania maszyn wirtualnych platformy Azure, zobacz temat [Azure Disk Encryption for Windows and Linux Azure Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) (Szyfrowanie dysków Azure dla maszyn wirtualnych platformy Azure z systemem Windows i Linux).
>
>

## <a name="install-and-configure-azure-powershell"></a>Instalowanie i konfigurowanie programu Azure PowerShell
Na komputerze musi być zainstalowany program Azure PowerShell w wersji 1.2.1 lub nowszej. Artykuł [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) zawiera opis wszystkich kroków aprowizacji komputera do pracy z programem Azure PowerShell. Najprostszym rozwiązaniem jest użycie Instalatora platformy sieci Web wspomnianego w tym artykule. Nawet jeśli program Azure PowerShell jest już zainstalowany, zainstaluj go ponownie przy użyciu Instalatora platformy sieci Web, aby dysponować najnowszą wersją programu Azure PowerShell.

## <a name="obtain-and-run-the-azure-disk-encryption-prerequisites-configuration-script"></a>Uzyskanie i uruchomienie skryptu konfiguracji wymagań wstępnych szyfrowania dysków Azure
Skrypt konfiguracji wymagań wstępnych szyfrowania dysków Azure umożliwia skonfigurowanie wszystkich wymagań wstępnych dotyczących szyfrowania maszyn wirtualnych platformy Azure.

1. Przejdź do strony GitHub, która zawiera [skrypt konfiguracji wymagań wstępnych szyfrowania dysków Azure](https://github.com/Azure/azure-powershell/blob/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1).
2. Na stronie GibHub kliknij przycisk **Raw** (Nieprzetworzone).
3. Użyj skrótu klawiaturowego **CTRL-A**, aby zaznaczyć cały tekst na stronie, a następnie naciśnij kombinację klawiszy **CTRL-C**, aby skopiować cały tekst ze strony do schowka.
4. Otwórz **Notatnik** i wklej skopiowany tekst do Notatnika.
5. Utwórz na dysku C: nowy folder o nazwie **AzureADEScript**.
6. Zapisz plik Notatnika — kliknij menu **Plik**, a następnie kliknij polecenie **Zapisz jako**. W polu tekstowym Nazwa pliku wprowadź ciąg **„ADEPrereqScript.ps1”** i kliknij przycisk **Zapisz**. (Pamiętaj, aby umieścić nazwę w cudzysłowie, ponieważ w przeciwnym razie plik zostanie zapisany z rozszerzeniem txt).

Po zapisaniu zawartości skryptu otwórz skrypt w środowisku PowerShell ISE:

1. W menu Start kliknij opcję **Cortana**. Zapytaj **Cortanę** o „PowerShell”, wpisując nazwę **PowerShell** w polu tekstowym wyszukiwania Cortany.
2. Kliknij prawym przyciskiem myszy pozycję **Windows PowerShell ISE** i kliknij opcję **Uruchom jako administrator**.
3. W oknie **Administrator: Windows PowerShell ISE** kliknij opcję **View** (Widok), a następnie kliknij pozycję **Show Script Pane** (Pokaż okienko skryptu).
4. Jeśli po prawej stronie okna jest widoczne okienko **Commands** (Polecenia), kliknij przycisk **„x”** w prawym górnym rogu okienka, aby je zamknąć. Jeśli tekst jest zbyt mały, aby go przeczytać, użyj skrótu klawiaturowego **CTRL+Add** („Add” odpowiada znakowi „+”). Jeśli tekst jest zbyt duży, użyj skrótu klawiaturowego **CTRL+Subtract** („Subtract” odpowiada znakowi „-”).
5. Kliknij menu **File** (Plik), a następnie kliknij pozycję **Open** (Otwórz). Przejdź do folderu **C:\AzureADEScript** i kliknij dwukrotnie skrypt **ADEPrereqScript**.
6. Zawartość skryptu **ADEPrereqScript** powinna teraz zostać wyświetlona w programie PowerShell ISE. Jest ona oznaczona kolorami, dzięki czemu różne składniki, takie jak polecenia, parametry i zmienne, są lepiej widoczne.

Zawartość na ekranie powinna wyglądać jak poniżej.

![Okno programu PowerShell ISE](./media/security-center-disk-encryption/security-center-disk-encryption-fig2.png)

Górne okienko jest nazywane „okienkiem skryptu”, a dolne okienko jest określane jako „konsola”. Terminy te będą używane w dalszej części artykułu.

## <a name="run-the-azure-disk-encryption-prerequisites-powershell-command"></a>Uruchamianie polecenia skryptu wymagań wstępnych szyfrowania dysków Azure w programie PowerShell
Po uruchomieniu skryptu wymagań wstępnych szyfrowania dysków Azure zostaną wyświetlone zapytania o następujące informacje:

* **Resource Group Name** (Nazwa grupy zasobów) — nazwa grupy zasobów, w której ma być umieszczony magazyn kluczy.  Jeśli grupa zasobów o podanej nazwie nie istnieje, zostanie utworzona nowa grupa zasobów o nazwie zgodnej z wprowadzoną. Jeśli masz już grupę zasobów, której chcesz użyć w tej subskrypcji, wprowadź nazwę tej grupy zasobów.
* **Key Vault Name** (Nazwa magazynu kluczy) — nazwa magazynu kluczy, w którym mają być umieszczone klucze szyfrowania. Jeśli magazyn kluczy o podanej nazwie nie istnieje, zostanie utworzony nowy magazyn kluczy o nazwie zgodnej z wprowadzoną. Jeśli masz już magazyn kluczy, którego chcesz użyć, wprowadź nazwę istniejącego magazynu kluczy.
* **Location** (Lokalizacja) — lokalizacja magazynu kluczy. Upewnij się, że magazyn kluczy i maszyny wirtualne, które mają być szyfrowane, znajdują się w tej samej lokalizacji. Jeśli nie znasz lokalizacji, w dalszej części tego artykułu znajdziesz opis kroków umożliwiających jej ustalenie.
* **Azure Active Directory Application Name** (Nazwa aplikacji usługi Azure Active Directory) — nazwa aplikacji usługi Azure Active Directory, która zostanie użyta do zapisu kluczy tajnych w magazynie kluczy. Jeśli taka aplikacja nie istnieje, zostanie utworzona nowa aplikacja o podanej nazwie. Jeśli masz już aplikację usługi Azure Active Directory, której chcesz użyć, wprowadź nazwę tej aplikacji usługi Azure Active Directory.

> [!NOTE]
> Jeśli zastanawiasz się, dlaczego należy utworzyć aplikację usługi Azure Active Directory, zapoznaj się z sekcją *Rejestrowanie aplikacji w usłudze Azure Active Directory* w artykule [Wprowadzenie do usługi Azure Key Vault](../key-vault/key-vault-get-started.md).
>
>

Wykonaj poniższe kroki, aby zaszyfrować maszynę wirtualną platformy Azure:

1. Jeśli program PowerShell ISE został zamknięty, otwórz wystąpienie programu PowerShell ISE z podwyższonym poziomem uprawnień. Jeśli program PowerShell ISE nie jest jeszcze otwarty, wykonaj instrukcje przedstawione wcześniej w tym artykule. Jeśli skrypt został zamknięty, otwórz plik **ADEPrereqScript.ps1**, klikając menu **File** (Plik) i polecenie **Open** (Otwórz), a następnie wybierając skrypt w folderze **c:\AzureADEScript**. W przypadku wykonywania instrukcji zawartych w artykule od początku przejdź po prostu do kolejnego kroku.
2. W konsoli programu PowerShell ISE (w dolnym okienku programu PowerShell ISE) zmień fokus na fokus lokalny skryptu, wpisując ciąg **cd c:\AzureADEScript**, po czym naciśnij klawisz **ENTER**.
3. Ustaw zasady wykonywania na komputerze, aby umożliwić uruchamianie skryptu. Wpisz w konsoli polecenie **Set-ExecutionPolicy Unrestricted**, a następnie naciśnij klawisz ENTER. Jeśli zostanie wyświetlone okno dialogowe z informacją o skutkach zmiany zasad wykonywania, kliknij opcję **Yes to all** (Tak dla wszystkich) lub **Yes** (Tak) (jeśli jest widoczna opcja **Yes to all**, wybierz tę opcję, jeśli opcja **Yes to all** nie jest wyświetlona, kliknij przycisk **Yes**).
4. Zaloguj się do konta platformy Azure. W konsoli wpisz polecenie **Connect-AzureRmAccount** i naciśnij klawisz **ENTER**. Zostanie wyświetlone okno dialogowe, w którym należy wprowadzić poświadczenia. (Upewnij się, że masz uprawnienia do zmiany maszyn wirtualnych — jeśli nie masz takich uprawnień, nie możesz zaszyfrować maszyn. Jeśli nie masz pewności, zapytaj właściciela subskrypcji lub administratora). Powinny być widoczne informacje dotyczące takich parametrów, jak **Environment**, **Account**, **TenantId**, **SubscriptionId** i **CurrentStorageAccount**. Skopiuj wartość parametru **SubscriptionId** do Notatnika. Będzie ona potrzebna w kroku 6.
5. Sprawdź subskrypcję, do której należy maszyna wirtualna, oraz jej lokalizację. Przejdź do witryny [https://portal.azure.com](ttps://portal.azure.com) i zaloguj się.  W lewej części strony kliknij pozycję **Virtual Machines**. Zobaczysz listę maszyn wirtualnych i subskrypcje, do których należą.

   ![Maszyny wirtualne](./media/security-center-disk-encryption/security-center-disk-encryption-fig3.png)
6. Wróć do programu PowerShell ISE. Ustaw kontekst subskrypcji, w którym skrypt będzie uruchamiany. W konsoli wpisz polecenie **Select-AzureRmSubscription –SubscriptionId <ID_subskrypcji>** (zastąp **<ID_subskrypcji>** swoim faktycznym identyfikatorem subskrypcji) i naciśnij klawisz **ENTER**. Zostaną wyświetlone informacje o dotyczące takich parametrów, jak Environment, **Account**, **TenantId**, **SubscriptionId** i **CurrentStorageAccount**.
7. Teraz można przystąpić do uruchomienia skryptu. Kliknij przycisk **Run Script** (Uruchom skrypt) lub naciśnij klawisz **F5** na klawiaturze.

   ![Wykonywanie skryptu programu PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig4.png)
8. Po wyświetleniu zapytania o parametr **resourceGroupName:** wprowadź nazwę *grupy zasobów*, której chcesz użyć, a następnie naciśnij klawisz **ENTER**. Jeśli nie masz grupy zasobów, wprowadź nazwę, której chcesz użyć dla nowej grupy. Jeśli masz już *grupę zasobów*, której chcesz użyć (np. taką, w której znajduje się maszyna wirtualna), wprowadź nazwę istniejącej grupy zasobów.
9. Po wyświetleniu zapytania o parametr **keyVaultName:** wprowadź nazwę *magazynu kluczy*, którego chcesz użyć, a następnie naciśnij klawisz ENTER. Jeśli nie masz magazynu kluczy, wprowadź nazwę, której chcesz użyć dla nowego magazynu. Jeśli masz już magazyn kluczy, którego chcesz użyć, wprowadź nazwę istniejącego *magazynu kluczy*.
10. Po wyświetleniu zapytania o parametr **location:** wprowadź nazwę lokalizacji, w której znajduje się maszyna wirtualna do zaszyfrowania, a następnie naciśnij klawisz **ENTER**. Jeśli nie pamiętasz lokalizacji, wróć do kroku 5.
11. Po wyświetleniu zapytania o parametr **aadAppName:** wprowadź nazwę aplikacji usługi *Azure Active Directory*, której chcesz użyć, następnie naciśnij klawisz **ENTER**. Jeśli nie masz aplikacji usługi Azure Active Directory, wprowadź nazwę, której chcesz użyć dla nowej aplikacji. Jeśli masz już *aplikację usługi Azure Active Directory*, której chcesz użyć, wprowadź nazwę istniejącej *aplikacji usługi Azure Active Directory*.
12. Zostanie wyświetlone okno dialogowe logowania. Podaj poświadczenia (logowanie zostało już raz wykonane, ale trzeba zalogować się ponownie).
13. Skrypt zostanie uruchomiony i po zakończeniu działania zostanie wyświetlony monit o skopiowanie wartości parametrów **aadClientID**, **aadClientSecret**, **diskEncryptionKeyVaultUrl** i **keyVaultResourceId**. Skopiuj poszczególne wartości do schowka i wklej je do Notatnika.
14. Wróć do programu PowerShell ISE, umieść kursor na końcu ostatniego wiersza i naciśnij klawisz **ENTER**.

Dane wyjściowe skryptu powinny wyglądać podobnie jak poniżej:

![Dane wyjściowe programu PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig5.png)

## <a name="encrypt-the-azure-virtual-machine"></a>Szyfrowanie maszyny wirtualnej platformy Azure
Teraz można przystąpić do szyfrowania maszyny wirtualnej. Jeśli maszyna wirtualna znajduje się w tej samej grupie zasobów, co magazyn kluczy, możesz przejść do sekcji z opisem kroków szyfrowania. Jeśli jednak maszyna wirtualna nie znajduje się w tej samej grupie zasobów, co magazyn kluczy, musisz wprowadzić następujące polecenie w konsoli w programie PowerShell ISE:

**$resourceGroupName = <’GZ_maszyny_wirtualnej’>**

Zastąp wartość **<GZ_maszyny_wirtualnej>** nazwą grupy zasobów, w której znajdują się maszyny wirtualne, ujętą w pojedynczy cudzysłów. Następnie naciśnij klawisz **ENTER**.
Aby sprawdzić, czy wprowadzono poprawną nazwę grupy zasobów, wpisz następujące polecenie w konsoli programu PowerShell ISE:

**$resourceGroupName**

Naciśnij klawisz **ENTER**. Powinna zostać wyświetlona nazwa grupy zasobów, w której znajdują się maszyny wirtualne. Na przykład:

![Dane wyjściowe programu PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig6.png)

### <a name="encryption-steps"></a>Kroki szyfrowania
Najpierw należy przekazać do programu PowerShell nazwę maszyny wirtualnej, którą chcesz zaszyfrować. W konsoli wpisz polecenie:

**$vmName = <’nazwa_maszyny_wirtualnej’>**

Zastąp wartość **<’nazwa_maszyny_wirtualnej’>** nazwą maszyny wirtualnej (ujętą w pojedynczy cudzysłów), a następnie naciśnij klawisz **ENTER**.

Aby sprawdzić, czy wprowadzono poprawną nazwę maszyny wirtualnej, wpisz polecenie:

**$vmName**

Naciśnij klawisz **ENTER**. Powinna zostać wyświetlona nazwa maszyny wirtualnej, którą chcesz zaszyfrować. Na przykład:

![Dane wyjściowe programu PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig7.png)

Istnieją dwie metody uruchamiania polecenia szyfrowania w celu zaszyfrowania wszystkich dysków na maszynie wirtualnej. Pierwsze rozwiązanie polega na wpisaniu następującego polecenia w konsoli programu PowerShell ISE:

~~~
Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType All
~~~

Po wpisaniu tego polecenia naciśnij klawisz **ENTER**.

Drugi sposób polega na kliknięciu w okienku skryptu (górne okienko programu PowerShell ISE) i przewinięciu w dół do dolnej części skryptu. Wyróżnij polecenie wymienione powyżej i kliknij je prawym przyciskiem myszy, a następnie kliknij pozycję **Run Selection** (Uruchom zaznaczenie) lub naciśnij klawisz **F8** na klawiaturze.

![Program PowerShell ISE](./media/security-center-disk-encryption/security-center-disk-encryption-fig8.png)

Niezależnie od użytej metody zostanie wyświetlone okno dialogowe z informacją, że ukończenie operacji potrwa 10–15 minut. Kliknij przycisk **Yes** (Tak).

W czasie trwania procesu szyfrowania możesz wrócić do witryny Azure Portal i sprawdzić stan maszyny wirtualnej. W lewej części strony kliknij pozycję **Virtual Machines**, a następnie w bloku **Virtual Machines** kliknij nazwę maszyny wirtualnej, którą szyfrujesz. W wyświetlonym bloku w obszarze **Stan** będzie widoczna wartość **Aktualizowanie**. Oznacza to, że szyfrowanie jest w toku.

![Więcej szczegółowych informacji o maszynie wirtualnej](./media/security-center-disk-encryption/security-center-disk-encryption-fig9.png)

Wróć do programu PowerShell ISE. Po zakończeniu działania skryptu wyświetlony zostanie widok jak poniżej.

![Dane wyjściowe programu PowerShell](./media/security-center-disk-encryption/security-center-disk-encryption-fig10.png)

Aby wykazać, że maszyna wirtualna jest teraz zaszyfrowana, wróć do witryny Azure Portal, a następnie kliknij pozycję **Maszyny wirtualne** po lewej stronie. Kliknij nazwę maszyny wirtualnej, która została zaszyfrowana. W bloku **Ustawienia** kliknij pozycję **Dyski**.

![Opcje ustawień](./media/security-center-disk-encryption/security-center-disk-encryption-fig11.png)

W bloku **Dyski** wyświetlana będzie informacja, że opcja **Szyfrowanie** ma wartość **Włączone**.

![Właściwości dysku](./media/security-center-disk-encryption/security-center-disk-encryption-fig12.png)

## <a name="next-steps"></a>Następne kroki
W tym dokumencie opisano sposób szyfrowania maszyny wirtualnej platformy Azure. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Monitorowanie kondycji zabezpieczeń w Centrum zabezpieczeń Azure](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów na platformie Azure.
* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w Centrum zabezpieczeń Azure](security-center-managing-and-responding-alerts.md) — informacje na temat reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Centrum zabezpieczeń Azure — często zadawane pytania](security-center-faq.md) — odpowiedzi na najczęstsze pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
