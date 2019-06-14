---
title: Szczegółowe komunikaty o błędach protokołu RDP dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Świadomość, że określone komunikaty o błędach, które można napotkać podczas próby użycia programu Podłączanie pulpitu zdalnego na maszynę wirtualną Windows na platformie Azure
keywords: Błąd połączenia zdalnego, błąd połączeń usług pulpitu zdalnego, nie można nawiązać połączenia z maszyną Wirtualną, rozwiązywania problemów usług pulpitu zdalnego
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: f4d733e29d2ba8213e1832f2c604b726283ab3e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318701"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Rozwiązywanie problemów z szczegółowe komunikaty o błędach protokołu RDP do maszyny Wirtualnej z systemem Windows na platformie Azure
Otrzymasz komunikat o błędzie, gdy za pomocą programu Podłączanie pulpitu zdalnego z maszyną wirtualną (VM) Windows na platformie Azure. Ten artykuł szczegółowo opisuje niektóre typowe komunikaty o błędach napotkano oraz rozwiązywanie problemów, aby je rozwiązać. Jeśli występują problemy z nawiązywaniem połączenia z maszyną wirtualną przy użyciu protokołu RDP ale czy nie występują określony komunikat o błędzie, zobacz [przewodnik rozwiązywania problemów dla pulpitu zdalnego](troubleshoot-rdp-connection.md).

Aby uzyskać informacje na określone komunikaty o błędach zobacz następujące tematy:

* [Sesja zdalna została rozłączona, ponieważ nie zdalnego pulpitu dostępnych serwerów licencji mogą udostępnić licencję](#rdplicense).
* [Pulpit zdalny nie może znaleźć komputera "name"](#rdpname).
* [Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędem zabezpieczeń lokalnych](#rdpauth).
* [Błąd zabezpieczeń Windows: Twoje poświadczenia nie działają](#wincred).
* [Ten komputer nie może połączyć się z komputerem zdalnym](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Sesja zdalna została rozłączona, ponieważ nie zdalnego pulpitu dostępnych serwerów licencji mogą udostępnić licencję.
Przyczyna: 120-dniowy okres prolongaty licencjonowania dla roli serwera usług pulpitu zdalnego utracił ważność i należy zainstalować licencji.

Jako obejście zapisać lokalną kopię pliku RDP z poziomu portalu, a następnie uruchom następujące polecenie w wierszu polecenia programu PowerShell do łączenia z. Ten krok powoduje wyłączenie licencjonowania dla właśnie tego połączenia:

        mstsc <File name>.RDP /admin

Jeśli więcej niż dwóch jednoczesnych połączeń usług pulpitu zdalnego z maszyną wirtualną nie jest potrzebna, można użyć Menedżera serwera, aby usunąć rolę serwera usług pulpitu zdalnego.

Aby uzyskać więcej informacji, zobacz wpis w blogu [maszyny Wirtualnej platformy Azure nie powiodło się "Nie serwerów usług pulpitu zdalnego licencji dostępnych"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Pulpit zdalny nie może znaleźć komputera "name".
Przyczyna: Klient pulpitu zdalnego na komputerze nie można rozpoznać nazwę komputera w ustawieniach plik RDP.

Możliwe rozwiązania:

* Jeśli jesteś w sieci intranet w organizacji, upewnij się, czy komputer ma dostęp do serwera proxy i wysłać do niej ruchu HTTPS.
* Jeśli korzystasz z lokalnie przechowywanego pliku RDP, spróbuj użyć jednego, który jest generowany przez portal. Ten krok zapewnia, że masz prawidłową nazwę DNS maszyny wirtualnej lub usługi w chmurze i port punktu końcowego maszyny wirtualnej. Poniżej przedstawiono przykładowy plik RDP, wygenerowany przez portal:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Ma część adresu ten plik protokołu RDP:

* W pełni kwalifikowana nazwa domeny usługi w chmurze, która zawiera maszyny Wirtualnej ("tailspin-azdatatier.cloudapp.net" w tym przykładzie).
* Port TCP zewnętrznego punktu końcowego ruchu pulpitu zdalnego (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędem zabezpieczeń lokalnych.
Przyczyna: Docelowa maszyna wirtualna nie może zlokalizować urzędu zabezpieczeń w część nazwy użytkownika poświadczeń.

Gdy Twoja nazwa użytkownika jest w formie *SecurityAuthority*\\*UserName* (przykład: CORP\User1) *SecurityAuthority* fragment to nazwa komputera maszyny Wirtualnej (dla urzędu zabezpieczeń lokalnych) lub nazwę domeny usługi Active Directory.

Możliwe rozwiązania:

* Jeśli konto jest kontem lokalnym na maszynie wirtualnej, upewnij się, że nazwa maszyny Wirtualnej została wpisana poprawnie.
* Jeśli konto znajduje się w domenie usługi Active Directory, Sprawdź pisownię nazwy domeny.
* Jeśli jest kontem domeny usługi Active Directory, a nazwa domeny została wpisana poprawnie, sprawdź, czy kontroler domeny jest dostępny w tej domenie. Jest to typowy problem w sieciach wirtualnych platformy Azure, zawierających kontrolery domeny kontroler domeny jest niedostępna, ponieważ nie został uruchomiony. Obejść ten problem można użyć konta administratora lokalnego, a nie konto domeny.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Błąd zabezpieczeń Windows: Twoje poświadczenia nie działają.
Przyczyna: Docelowa maszyna wirtualna nie może zweryfikować swojej nazwy konta i hasła.

Komputer z systemem Windows można zweryfikować poświadczeń konta lokalnego lub konta domeny.

* Dla kont lokalnych, należy użyć *ComputerName*\\*UserName* składni (przykład: SQL1\Admin4798).
* Dla kont domeny, należy użyć *nazwa_domeny*\\*UserName* składni (przykład: CONTOSO\peterodman).

Jeśli zostanie promowany maszyny Wirtualnej z kontrolerem domeny w nowym lesie usługi Active Directory, konto administratora lokalnego, który podpisany przy użyciu jest konwertowany na równoważne konta z tego samego hasła w nowym lesie i domenie. Konto lokalne jest usuwany.

Na przykład jeśli zalogowany za pomocą konta lokalnego DC1\DCAdmin, a następnie wspierać maszynę wirtualną jako kontroler domeny w nowym lesie w domenie corp.contoso.com, konta lokalnego DC1\DCAdmin zostaje usunięta, a nowe konto domeny (CORP\DCAdmin) tworzone przy użyciu tego samego hasła.

Upewnij się, że nazwa konta jest nazwa maszyny wirtualnej można sprawdzić jako prawidłowego konta i że hasło jest prawidłowe.

Jeśli musisz zmienić hasło konta administratora lokalnego, zobacz [jak zresetować hasło lub usługę Pulpit zdalny dla maszyn wirtualnych Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Ten komputer nie może połączyć się z komputerem zdalnym.
Przyczyna: Konta, które służy do łączenia nie ma praw logowania usług pulpitu zdalnego.

Każdy komputer Windows ma pulpitu zdalnego użytkownicy grupy lokalnej, która zawiera konta i grupy, które można zdalnie Zaloguj się do niego. Członkowie grupy Administratorzy lokalni również mają dostęp, nawet jeśli te konta nie są wymienione w lokalnej grupie Użytkownicy pulpitu zdalnego. W przypadku komputerów przyłączonych do domeny do lokalnej grupy administratorów zawiera także Administratorzy domeny w domenie.

Upewnij się, że konto, którego używasz do łączenia z uprawnieniami pulpitu zdalnego logowania. Obejść ten problem należy użyć domeny lub lokalnego konta administratora połączenia za pośrednictwem usług pulpitu zdalnego. Aby dodać odpowiednie konto do grupy lokalnej Użytkownicy pulpitu zdalnego, należy użyć przystawki programu Microsoft Management Console (**Narzędzia systemowe > Użytkownicy i grupy lokalne > grupy > Użytkownicy pulpitu zdalnego**).

## <a name="next-steps"></a>Kolejne kroki
Jeśli żadna z tych błędów wystąpił i ma nieznany problem z połączeniem się przy użyciu protokołu RDP, zobacz [przewodnik rozwiązywania problemów dla pulpitu zdalnego](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Rozwiązywanie problemów podczas uzyskiwania dostępu do aplikacji działających na maszynie Wirtualnej, dla [Rozwiązywanie problemów z dostępem do aplikacji uruchamianej na Maszynie wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Jeśli występują problemy przy użyciu protokołu Secure Shell (SSH), aby nawiązać połączenie z maszyny Wirtualnej z systemem Linux na platformie Azure, zobacz [Rozwiązywanie problemów z połączeń protokołu SSH z maszyny Wirtualnej z systemem Linux na platformie Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

