---
title: Komunikaty o błędach protokołu RDP dla maszyn wirtualnych platformy Azure | Dokumenty firmy Microsoft
description: Opis określonych komunikatów o błędach, które mogą pojawić się podczas próby użycia połączenia pulpitu zdalnego z maszyną wirtualną systemu Windows na platformie Azure
keywords: Błąd pulpitu zdalnego,błąd połączenia pulpitu zdalnego,nie można połączyć się z maszyną wirtualną,rozwiązywanie problemów z pulpitem zdalnym
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 851c5eb4ebfee4e4a4836a07b51578dd2b0c68cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266873"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Rozwiązywanie problemów z określonymi komunikatami o błędach protokołu RDP dla maszyny wirtualnej z systemem Windows na platformie Azure
Podczas korzystania z połączenia pulpitu zdalnego z maszyną wirtualną systemu Windows (VM) na platformie Azure może pojawić się określony komunikat o błędzie. W tym artykule opisano niektóre z bardziej typowych komunikatów o błędach napotkanych, wraz z krokami rozwiązywania problemów, aby je rozwiązać. Jeśli występują problemy z połączeniem się z maszyną wirtualną przy użyciu protokołu RDP, ale nie występują określone komunikaty o błędzie, zobacz [przewodnik rozwiązywania problemów z pulpitem zdalnym](troubleshoot-rdp-connection.md).

Aby uzyskać informacje na temat określonych komunikatów o błędach, zobacz następujące informacje:

* [Sesja zdalna została rozłączona, ponieważ nie ma dostępnych serwerów licencji usług pulpitu zdalnego, aby zapewnić licencję](#rdplicense).
* [Pulpit zdalny nie może znaleźć "nazwy" komputera](#rdpname).
* [Wystąpił błąd uwierzytelniania. Nie można skontaktować się z lokalnym urzędem bezpieczeństwa](#rdpauth).
* [Błąd zabezpieczeń systemu Windows: poświadczenia nie działały](#wincred).
* [Ten komputer nie może połączyć się z komputerem zdalnym](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Sesja zdalna została rozłączona, ponieważ nie ma dostępnych serwerów licencji usług pulpitu zdalnego, aby zapewnić licencję.
Przyczyna: 120-dniowy okres prolongaty licencjonowania dla roli Serwer pulpitu zdalnego wygasł i należy zainstalować licencje.

Aby obejść ten problem, zapisz lokalną kopię pliku RDP z portalu i uruchom to polecenie w wierszu polecenia programu PowerShell, aby się połączyć. Ten krok wyłącza licencjonowanie tylko dla tego połączenia:

        mstsc <File name>.RDP /admin

Jeśli w rzeczywistości nie potrzebujesz więcej niż dwóch jednoczesnych połączeń pulpitu zdalnego z maszyną wirtualną, możesz użyć Menedżera serwera, aby usunąć rolę serwera pulpitu zdalnego.

Aby uzyskać więcej informacji, zobacz wpis w blogu [Azure VM kończy się niepowodzeniem z "Brak serwerów licencji pulpitu zdalnego dostępne"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Pulpit zdalny nie może znaleźć "nazwy" komputera.
Przyczyna: Klient pulpitu zdalnego na komputerze nie może rozpoznać nazwy komputera w ustawieniach pliku RDP.

Możliwe rozwiązania:

* Jeśli korzystasz z intranetu organizacji, upewnij się, że komputer ma dostęp do serwera proxy i może wysyłać do niego ruch HTTPS.
* Jeśli używasz lokalnie przechowywanego pliku RDP, spróbuj użyć pliku wygenerowanego przez portal. Ten krok gwarantuje, że masz poprawną nazwę DNS dla maszyny wirtualnej lub usługi w chmurze i portu punktu końcowego maszyny wirtualnej. Oto przykładowy plik RDP wygenerowany przez portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Część adresowa tego pliku RDP zawiera:

* W pełni kwalifikowana nazwa domeny usługi w chmurze, która zawiera maszynę wirtualną ("tailspin-azdatatier.cloudapp.net" w tym przykładzie).
* Zewnętrzny port TCP punktu końcowego dla ruchu pulpitu zdalnego (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Wystąpił błąd uwierzytelniania. Nie można skontaktować się z Lokalnym Urzędem Bezpieczeństwa.
Przyczyna: Docelowa maszyna wirtualna nie może zlokalizować urzędu zabezpieczeń w części nazwy użytkownika poświadczeń.

Gdy nazwa użytkownika znajduje się w formularzu *SecurityAuthority*\\*UserName* (przykład: CORP\User1), część *SecurityAuthority* jest nazwą komputera maszyny Wirtualnej (dla lokalnego urzędu zabezpieczeń) lub nazwą domeny usługi Active Directory.

Możliwe rozwiązania:

* Jeśli konto jest lokalne dla maszyny Wirtualnej, upewnij się, że nazwa maszyny Wirtualnej jest poprawnie wpisany.
* Jeśli konto znajduje się w domenie usługi Active Directory, sprawdź pisownię nazwy domeny.
* Jeśli jest to konto domeny usługi Active Directory, a nazwa domeny jest poprawnie wpisana, sprawdź, czy kontroler domeny jest dostępny w tej domenie. Jest to typowy problem w sieciach wirtualnych platformy Azure, które zawierają kontrolery domeny, że kontroler domeny jest niedostępny, ponieważ nie został uruchomiony. Aby obejść ten problem, możesz użyć konta administratora lokalnego zamiast konta domeny.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Błąd zabezpieczeń systemu Windows: poświadczenia nie działały.
Przyczyna: Docelowa maszyna wirtualna nie może zweryfikować nazwy konta i hasła.

Komputer z systemem Windows może sprawdzać poprawność poświadczeń konta lokalnego lub konta domeny.

* W przypadku kont lokalnych należy użyć składni*Nazwa użytkownika* *komputera*\\(przykład: SQL1\Admin4798).
* W przypadku kont domeny należy użyć składni*Nazwa użytkownika* *nazwy domeny*\\(przykład: CONTOSO\peterodman).

Jeśli maszyna wirtualna została wypromowana na kontroler domeny w nowym lesie usługi Active Directory, konto administratora lokalnego, za pomocą którego zalogowano się, zostanie przekonwertowane na równoważne konto z tym samym hasłem w nowym lesie i domenie. Konto lokalne zostanie następnie usunięte.

Na przykład po zalogowaniu się przy za pomocą konta lokalnego DC1\DCAdmin, a następnie promowaniu maszyny wirtualnej jako kontrolera domeny w nowym lesie dla domeny corp.contoso.com, konto lokalne DC1\DCAdmin zostanie usunięte, a nowe konto domeny (CORP\DCAdmin) zostanie usunięte, a nowe konto domeny (CORP\DCAdmin) zostanie usunięte, a nowe konto domeny (CORP\DCAdmin) zostanie usunięte utworzone przy tym samym haśle.

Upewnij się, że nazwa konta jest nazwą, którą maszyna wirtualna może zweryfikować jako prawidłowe konto i że hasło jest poprawne.

Jeśli chcesz zmienić hasło konta administratora lokalnego, zobacz [Jak zresetować hasło lub usługę pulpitu zdalnego dla maszyn wirtualnych systemu Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Ten komputer nie może połączyć się z komputerem zdalnym.
Przyczyna: Konto używane do nawiązywania połączenia nie ma praw logowania do pulpitu zdalnego.

Każdy komputer z systemem Windows ma lokalną grupę użytkowników pulpitu zdalnego, która zawiera konta i grupy, które mogą się do niego logować zdalnie. Członkowie grupy administratorów lokalnych również mają dostęp, nawet jeśli te konta nie są wymienione w grupie lokalnej użytkownicy pulpitu zdalnego. W przypadku komputerów przyłączonych do domeny grupa administratorów lokalnych zawiera również administratorów domeny.

Upewnij się, że konto, którego używasz do nawiązywania połączenia, ma prawa logowania do pulpitu zdalnego. Aby połączyć się za pomocą pulpitu zdalnego, użyj konta administratora domeny lub lokalnego. Aby dodać żądane konto do grupy lokalnej użytkownicy pulpitu zdalnego, użyj przystawki Microsoft Management Console **(Narzędzia systemowe > użytkownicy i grupy lokalne > grupy > użytkownicy pulpitu zdalnego).**

## <a name="next-steps"></a>Następne kroki
Jeśli nie wystąpił żaden z tych błędów i wystąpił nieznany [troubleshooting guide for Remote Desktop](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)problem z połączeniem z prok.

* Aby uzyskać instrukcje rozwiązywania problemów z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie Wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Jeśli masz problemy z używaniem secure shell (SSH) do łączenia się z maszyną wirtualną z systemem Linux na platformie Azure, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną z systemem Linux na platformie Azure.](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

