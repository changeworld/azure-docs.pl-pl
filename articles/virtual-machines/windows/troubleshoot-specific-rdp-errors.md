---
title: Określone komunikaty o błędach protokołu RDP dla maszyn wirtualnych platformy Azure | Dokumentacja firmy Microsoft
description: Zrozumienie określone komunikaty o błędach, które można napotkać podczas próby użycia Podłączanie pulpitu zdalnego do maszyny wirtualnej systemu Windows na platformie Azure
keywords: Błąd pulpitu zdalnego, błąd połączeń usług pulpitu zdalnego, nie można połączyć z maszyną wirtualną, rozwiązywania problemów pulpitu zdalnego
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
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 4d13d32e0269be3715a18484157519e1a0556ade
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Rozwiązywanie problemów z określone komunikaty o błędach protokołu RDP do maszyny Wirtualnej systemu Windows na platformie Azure
W trakcie przy użyciu połączenia pulpitu zdalnego z maszyną wirtualną (VM) systemu Windows na platformie Azure może zostać wyświetlony komunikat o błędzie. Ten artykuł zawiera szczegóły dotyczące niektórych bardziej typowe komunikaty o błędach napotkano wraz z czynności pozwalające rozwiązać je. Jeśli występują problemy dotyczące nawiązywania połączenia z maszyną Wirtualną za pomocą protokołu RDP ale czy nie występują komunikat o błędzie, zobacz [Podręcznik rozwiązywania problemów dotyczących usług pulpitu zdalnego](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Aby uzyskać informacje na określone komunikaty o błędach zobacz następujące tematy:

* [Sesja zdalna została rozłączona, ponieważ nie nie zdalne serwery licencji usług pulpitu do dyspozycji licencji](#rdplicense).
* [Pulpit zdalny nie może odnaleźć komputera "name"](#rdpname).
* [Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędu zabezpieczeń lokalnych](#rdpauth).
* [Błąd zabezpieczeń systemu Windows: Twoje poświadczenia nie działają](#wincred).
* [Ten komputer nie może połączyć się z komputerem zdalnym](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>Sesja zdalna została rozłączona, ponieważ nie nie zdalne serwery licencji usług pulpitu do dyspozycji licencji.
Przyczyna: 120-dniowy okres prolongaty licencjonowania dla roli serwera usług pulpitu zdalnego wygasł i musisz zainstalować licencji.

Jako obejście zapisać lokalną kopię pliku RDP z portalu i uruchom to polecenie w wierszu polecenia programu PowerShell do połączenia. Ten krok powoduje wyłączenie Licencjonowanie tylko tego połączenia:

        mstsc <File name>.RDP /admin

Jeśli nie potrzebujesz faktycznie więcej niż dwóch jednoczesnych połączeń usług pulpitu zdalnego do maszyny Wirtualnej, należy usunąć rolę serwera usług pulpitu zdalnego przy użyciu Menedżera serwera.

Aby uzyskać więcej informacji, zobacz w blogu [maszyny Wirtualnej platformy Azure nie powiodło się "Nie serwerów usług pulpitu zdalnego licencji dostępnych"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>Pulpit zdalny nie może odnaleźć komputera "name".
Przyczyna: Klient pulpitu zdalnego na komputerze nie można rozpoznać nazwy komputera w ustawieniach pliku RDP.

Możliwe rozwiązania:

* Jeśli jesteś w intranecie firmy, upewnij się, że komputer ma dostęp do serwera proxy i może wysyłać ruch HTTPS do niej.
* Jeśli korzystasz z lokalnie przechowywanego pliku RDP, spróbuj użyć ten, który jest generowany przez portal. Ten krok zapewnia poprawną nazwę DNS maszyny wirtualnej lub usługi w chmurze i port punktu końcowego maszyny wirtualnej. Oto przykładowy plik RDP, generowane przez portalu:
  
        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

Ma adres część tego pliku RDP:

* W pełni kwalifikowaną nazwę usługi w chmurze, która zawiera maszyny Wirtualnej ("tailspin-azdatatier.cloudapp.net" w tym przykładzie).
* Port TCP zewnętrznego punktu końcowego dla ruchu pulpitu zdalnego (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Wystąpił błąd uwierzytelniania. Nie można skontaktować się z urzędu zabezpieczeń lokalnych.
Przyczyna: Docelowy maszyny Wirtualnej nie może zlokalizować urzędu zabezpieczeń w części zawierającej nazwę użytkownika poświadczeń.

Jeśli nazwa użytkownika jest w formie *SecurityAuthority*\\*UserName* (przykład: CORP\User1), *SecurityAuthority* fragment jest nazwa komputera maszyny Wirtualnej (dla urzędu zabezpieczeń lokalnych) lub nazwy domeny usługi Active Directory.

Możliwe rozwiązania:

* Jeśli konto jest kontem lokalnym na maszynie wirtualnej, upewnij się, że nazwa maszyny Wirtualnej jest poprawna.
* Jeśli konto znajduje się w domenie usługi Active Directory, Sprawdź pisownię nazwy domeny.
* Jeśli konto domeny usługi Active Directory i nazwa domeny została wpisana poprawnie, sprawdź, czy kontroler domeny jest dostępny w tej domenie. Jest typowym problemem w sieci wirtualnych platformy Azure, zawierających kontrolery domeny, że kontroler domeny jest niedostępna, ponieważ nie została uruchomiona. Jako rozwiązanie alternatywne można użyć konta administratora lokalnego zamiast konta domeny.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Błąd zabezpieczeń systemu Windows: Twoje poświadczenia nie działają.
Przyczyna: Docelowy maszyny Wirtualnej nie może zweryfikować nazwę konta i hasło.

Komputer z systemem Windows można zweryfikować poświadczeń konta lokalnego lub konta domeny.

* Dla kont lokalnych, użyj *ComputerName*\\*UserName* składni (przykład: SQL1\Admin4798).
* Dla kont domeny, użyj *DomainName*\\*UserName* składni (przykład: CONTOSO\peterodman).

Jeśli zostały awansowane maszyny Wirtualnej do kontrolera domeny w nowym lesie usługi Active Directory, który podpisany przy użyciu konta administratora lokalnego jest konwertowana na równoważne konta z tego samego hasła w nowym lesie i domenie. Konto lokalne jest usuwany.

Na przykład jeśli jest zalogowany za pomocą konta lokalnego DC1\DCAdmin, a następnie podwyższony maszynę wirtualną jako kontroler domeny w nowym lesie dla domeny corp.contoso.com, konta lokalnego DC1\DCAdmin zostaje usunięta, a nowe konto domeny (CORP\DCAdmin) jest tworzony przy użyciu tego samego hasła.

Upewnij się, że nazwa konta jest nazwę maszyny wirtualnej można sprawdzić jako prawidłowe konto i że hasło jest prawidłowe.

Jeśli musisz zmienić hasło konta administratora lokalnego, zobacz [sposób resetowania hasła lub usługi pulpitu zdalnego dla maszyn wirtualnych systemu Windows](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Ten komputer nie może połączyć się z komputerem zdalnym.
Przyczyna: Konto, które służy do łączenia nie ma pulpitu zdalnego logowania praw.

Każdy komputer z systemem Windows ma pulpitu zdalnego użytkownicy grupy lokalnej, która zawiera konta i grupy, które można zdalnie Zaloguj się do niego. Członkowie grupy Administratorzy lokalni mają także dostęp, nawet jeśli te konta nie są wyświetlane w lokalnej grupie Użytkownicy pulpitu zdalnego. W przypadku komputerów przyłączonych do domeny do lokalnej grupy administratorów zawiera także Administratorzy domeny w domenie.

Upewnij się, że konto, którego używasz nawiązać połączenia z uprawnieniami pulpitu zdalnego logowania. Jako obejście za pomocą domeny lub lokalnego konta administratora połączenia za pośrednictwem pulpitu zdalnego. Aby dodać odpowiednie konto do lokalnej grupy użytkowników pulpitu zdalnego, należy użyć przystawki programu Microsoft Management Console (**Narzędzia systemowe > Użytkownicy i grupy lokalne > grupy > Użytkownicy pulpitu zdalnego**).

## <a name="next-steps"></a>Kolejne kroki
Jeśli żaden z tych błędów wystąpił i konieczne jest nieznany problem z połączeniem się przy użyciu protokołu RDP, zobacz [Podręcznik rozwiązywania problemów dotyczących usług pulpitu zdalnego](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

* Kroki podczas uzyskiwania dostępu do aplikacji działających na maszynie Wirtualnej, zobacz [Rozwiązywanie problemów z dostępem do aplikacji uruchomionej na maszynie Wirtualnej platformy Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Jeśli występują problemy przy użyciu protokołu Secure Shell (SSH) do połączenia z maszyną wirtualną systemu Linux na platformie Azure, zobacz [połączeń Rozwiązywanie problemów z SSH z maszyną wirtualną systemu Linux na platformie Azure](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

