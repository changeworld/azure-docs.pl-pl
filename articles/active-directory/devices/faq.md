---
title: Zarządzanie urządzeniami w usłudze Azure Active Directory — często zadawane pytania | Dokumentacja firmy Microsoft
description: Usługa Azure Active Directory Zarządzanie urządzeniami — często zadawane pytania.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: a0cfd65aa2444956336e5363d20acab61a404c68
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309182"
---
# <a name="azure-active-directory-device-management-faq"></a>Zarządzanie urządzeniami w usłudze Azure Active Directory — często zadawane pytania

**PYT.: Czy ostatnio zarejestrowane urządzenia. Dlaczego nie widzę urządzenia w obszarze Moje informacje o użytkowniku w witrynie Azure portal? Lub dlaczego jest właściciel urządzenia oznaczony jako n/d dla urządzeń z hybrydowego przyłączony Azure AD?**
 **Odp.:** Urządzenia z systemem Windows 10, które są hybrydowe przyłączone do usługi Azure AD nie są wyświetlane w obszarze urządzeń użytkowników.
Należy użyć widoku wszystkich urządzeń w witrynie Azure portal. Możesz również użyć programu PowerShell [Get MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) polecenia cmdlet.

Następujące urządzenia zostaną wyświetlone w obszarze urządzenia użytkownika:

- Przyłączone do wszystkich urządzeń osobistych, które nie są hybrydowej usługi Azure AD. 
- Wszystkie inne niż - Windows 10 / urządzeń z systemem Windows Server 2016.
- Wszystkie urządzenia inne niż Windows 

--- 

**PYT.: Jak sprawdzić stanu rejestracji urządzenia klienta jest?**

**ODP.:** Możesz użyć witryny Azure portal, przejdź do wszystkich urządzeń i wyszukaj urządzenia przy użyciu identyfikatora urządzenia. Sprawdź wartości w kolumnie Typ sprzężenia. Czasami urządzenie może zostały resetowania lub odtworzony z obrazu. Dlatego bardzo ważne jest również za sprawdzanie stanu rejestracji urządzenia na urządzeniu:

- W przypadku systemu Windows 10 i Windows Server 2016 lub nowszym należy uruchomić dsregcmd.exe parametru/status.
- Dla wcześniejszych wersji systemu operacyjnego należy uruchomić "%programFiles%\Microsoft Join\autoworkplace.exe obszar roboczy"

---

**PYT.: Zobacz rekordem urządzenia w obszarze informacje o użytkowniku w witrynie Azure portal i można wyświetlić stan, jak zarejestrować się na urządzeniu. Jestem I poprawnie skonfigurowane do korzystania z dostępu warunkowego?**

**ODP.:** Stan dołączania urządzenia, dostarczanej przez deviceID, musi zgodne z tym w usłudze Azure AD i spełniać wszystkie kryteria oceny dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [wymagają zarządzanych urządzeń, aby uzyskać dostęp do aplikacji w chmurze przy użyciu dostępu warunkowego](../conditional-access/require-managed-devices.md).

---

**PYT.: Usunięto w witrynie Azure portal lub za pomocą programu Windows PowerShell, ale stan lokalnego na urządzeniu jest wyświetlany komunikat jest nadal zarejestrowany?**

**ODP.:** Jest to celowe. Urządzenie nie będzie dostępu do zasobów w chmurze. 

Jeśli chcesz ponownie zarejestrować ponownie, akcji ręcznych musi być podejmowane na urządzeniu. 

Aby wyczyścić stanu dołączania do, z systemem Windows 10 i Windows Server 2016, które są lokalnej usługi AD przyłączonych do domeny:

1.  Otwórz wiersz polecenia jako administrator.

2.  Typ `dsregcmd.exe /debug /leave`

3.  Wyloguj się i zaloguj się w celu wyzwolenia zaplanowane zadanie, które rejestruje urządzenie z usługą Azure AD ponownie. 

Dla wersji systemu operacyjnego Windows niższego poziomu, które znajdują się na obszarze AD przyłączonych do domeny:

1.  Otwórz wiersz polecenia jako administrator.
2.  Wpisz polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Wpisz polecenie `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**PYT.: Dlaczego są wyświetlane urządzenia zduplikowanych wpisów w witrynie Azure portal?**

**ODP.:**

-   Dla systemu Windows 10 i Windows Server 2016 Jeśli istnieją ponawiane próby odłączenia, a następnie ponowne dołączenie tego samego urządzenia mogą istnieć zduplikowane wpisy. 

-   Jeśli używano dodać pracy konta firmowego lub szkolnego każdego użytkownika systemu windows, który używa Dodaj pracy lub nauki utworzy nowy rekord urządzenia o takiej samej nazwie urządzenia.

-   Dla wersji systemu operacyjnego Windows niższego poziomu, które znajdują się na obszarze AD przyłączonych do domeny przy użyciu automatycznej rejestracji utworzy nowy rekord urządzenia z taką samą nazwę urządzenia dla każdego użytkownika domeny, który loguje się do urządzenia. 

-   Komputerze dołączonym do usługi Azure AD, który wyczyszczeniu, ponownej instalacji i ponownie przyłączone o takiej samej nazwie, pojawią się jako inny rekord o takiej samej nazwie urządzenia.

---

**PYT.: Dlaczego może użytkownik nadal dostęp do zasobów z urządzenia, wyłączenia w witrynie Azure portal?**

**ODP.:** Może potrwać do godziny odwołania mają być stosowane.

>[!Note] 
>Dla zarejestrowanych urządzeń zaleca się wyczyszczenie urządzenia, aby upewnić się, że użytkownicy nie mogą uzyskać dostęp do zasobów. Aby uzyskać więcej informacji, zobacz [rejestrowania urządzeń na potrzeby zarządzania przez usługę Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Funkcja Azure AD Join — często zadawane pytania

**PYT.: Jak I odłączenia urządzenia z systemem Azure AD dołączono lokalnie na urządzeniu?**

**ODP.:** 
- Do hybrydowej usługi Azure AD urządzeń w miejscu pracy upewnij się wyłączyć rejestracji automatycznej, tak aby zaplanowane zadanie nie ponownie zarejestrować urządzenie. Następnie otwórz wiersz polecenia jako administrator i wpisz `dsregcmd.exe /debug /leave`. To polecenie można również uruchomić jako skrypt na wielu urządzeniach odłączyć zbiorczo.

- Dla czystych dołączono usługi Azure AD do urządzeń, upewnij się, w trybie offline administrator lokalny, konto lub utwórz je, ponieważ nie będzie można zalogować się przy użyciu żadnych poświadczeń użytkownika usługi Azure AD. Następnie przejdź do **ustawienia** > **kont** > **dostęp do zasobów służbowych**. Wybierz swoje konto i kliknij **rozłączenia**. Postępuj zgodnie z monitami i podaj poświadczenia administratora lokalnego po wyświetleniu monitu. Uruchom ponownie urządzenie, aby ukończyć proces odłączania.

---

**PYT.: Moi użytkownicy zalogować się do urządzeń przyłączonych do usługi Azure AD, które zostały usunięte lub wyłączone w usłudze Azure AD?**
 **Odp.:** Tak. Windows ma pamięci podręcznej funkcji logowania, aby umożliwić, wcześniej zalogowani użytkownicy szybki dostęp pulpitu, nawet bez połączenia z siecią. Gdy urządzenie jest usunięte lub wyłączone w usłudze Azure AD, nie jest znany dla urządzeń Windows. Dlatego wcześniej zarejestrowany użytkownicy nadal mogą uzyskać dostęp do pulpitu z pamięci podręcznej logowania. Jednak ponieważ urządzenie jest usuwane lub wyłączona, użytkownicy nie mogą korzystać wszystkie zasoby chronione przez dostęp warunkowy oparty na urządzeniu. 

Użytkownicy, którzy jeszcze nie już zalogowany nie może uzyskać dostęp do urządzenia, się bez logowania pamięci podręcznej włączone dla nich. 

---

**PYT.: Można wyłączonych lub usuniętych użytkowników logowania się na urządzeniach przyłączonych do usługi Azure AD**
 **Odp.:** Tak, ale tylko przez ograniczony czas. Gdy użytkownik jest usunięte lub wyłączone w usłudze Azure AD, nie od razu wiadomo na urządzeniu Windows. Dlatego wcześniej zarejestrowany użytkownicy mogą uzyskiwać dostęp do pulpitu z pamięci podręcznej logowania. Gdy urządzenie jest świadome danych stanu użytkownika (zwykle w mniej niż 4 godziny), Windows blokuje tych użytkownikom uzyskiwanie dostępu do pulpitu. Użytkownik jest usunięte lub wyłączone w usłudze Azure AD, wszystkie tokeny zostanie odwołana, dlatego nie mają dostępu do żadnych zasobów. 

Usunięto lub wyłączonych użytkowników, którzy nie zostały wcześniej zarejestrowane w nie może uzyskać dostępu urządzenia, się bez logowania pamięci podręcznej włączone dla nich. 

---

**PYT.: Moje użytkownicy nie może wyszukać drukarki z usługi Azure AD urządzeń w miejscu pracy. Jak włączyć drukowanie z usługi Azure AD urządzeń w miejscu pracy?**

**ODP.:** Do wdrażania drukarek dla usługi Azure AD urządzeń w miejscu pracy, zobacz [drukowania chmury hybrydowej](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Należy na lokalnym serwerze systemu Windows do wdrożenia drukowania chmury hybrydowej. Oparte na chmurze usługi drukowania nie jest obecnie dostępna. 

---

**PYT.: Jak połączyć do zdalnej usługi Azure AD dołączonym do urządzenia?**
 **Odp.:** Zapoznaj się z artykułem https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc Aby uzyskać szczegółowe informacje.

---

**PYT.: Dlaczego moja użytkownicy widzą "Nie można dostać się tam z tego miejsca"?**

**ODP.:** Jeśli skonfigurowano niektóre zasady dostępu warunkowego będą musieli stan określonego urządzenia, a urządzenie nie spełnia kryteria, użytkownicy są zablokowane i ten komunikat jest wyświetlany. Oceń zasady dostępu warunkowego i upewnij się, że urządzenie jest w stanie spełnić kryteria, aby uniknąć tego komunikatu.

---

**PYT.: Dlaczego niektóre z moich użytkowników czy nie MFA monity w usłudze Azure AD uzyskasz przyłączonych do urządzeń?**

**ODP.:** Jeśli użytkownik łączy lub rejestruje urządzenie w usłudze Azure AD przy użyciu uwierzytelniania wieloskładnikowego, samo urządzenie staną się zaufanych czynnika dla określonego użytkownika. Następnie zawsze wtedy, gdy ten sam użytkownik loguje się do urządzenia i uzyskuje dostęp do aplikacji, usługi Azure AD uwzględnia urządzenia jako drugiego składnika i umożliwia użytkownikowi uzyskiwanie dostępu do swoich aplikacji bez dodatkowych monitów uwierzytelniania Wieloskładnikowego. To zachowanie nie ma zastosowania do żadnego innego użytkownika, logując się do tego urządzenia, dzięki czemu wszystkim innym użytkownikom uzyskiwanie dostępu do tego urządzenia nadal jest proszony o żądania uwierzytelniania MFA przed uzyskaniem dostępu do aplikacji, które wymagają usługi MFA.

---

**PYT.: Dlaczego otrzymuję komunikat "Nazwa użytkownika lub hasło jest niepoprawne" dla urządzenia, które mogę po prostu przyłączył się do usługi Azure AD?**

**ODP.:** W tym scenariuszu typowe przyczyny są następujące:

- Poświadczenia użytkownika nie są już prawidłowe.

- Komputer nie może nawiązać połączenia z usługą Azure Active Directory. Sprawdź, czy wszystkie problemy z połączeniem sieciowym.

- Federacyjnych logowań będą wymagane serwerze federacyjnym do obsługi protokołu WS-Trust punktów końcowych, włączona i jest dostępny. 

- Włączono przekazywania uwierzytelniania, a użytkownik ma hasło tymczasowe, które musi zostać zmienione podczas logowania.

---

**PYT.: Dlaczego widzę "Niestety... Wystąpił błąd!" okno dialogowe, gdy próbuję, czy usługa Azure AD join komputera?**

**ODP.:** Jest to wynik konfigurowania rejestracji w usłudze Azure Active Directory przy użyciu usługi Intune. Upewnij się, że użytkownik próby wykonania sprzężenia Azure AD ma poprawne przypisano licencji usługi Intune. Aby uzyskać więcej informacji, zobacz [skonfigurować zarządzanie urządzeniami Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**PYT.: Dlaczego próba usługi Azure AD join awarii komputera mimo że nie mogę uzyskać informacje o błędzie?**

**ODP.:** Prawdopodobną przyczyną jest to, że użytkownik jest zalogowany do urządzenia przy użyciu konta wbudowanego konta administratora lokalnego. Przed użyciem usługi Azure Active Directory Join, aby ukończyć instalację, należy utworzyć innego konta lokalnego. 

---

## <a name="hybrid-azure-ad-join-faq"></a>Często zadawane pytania hybrydowe przyłączanie do usługi Azure AD

**PYT.: Gdzie mogę znaleźć, rozwiązywanie problemów z informacji dotyczących diagnozowania niepowodzeń przyłączanie do hybrydowej usługi Azure AD?**

**ODP.:** Aby uzyskać informacje dotyczące rozwiązywania problemów, zobacz:

- [Rozwiązywanie problemów z autorejestracją domeny komputery przyłączone do usługi Azure AD — system Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)

- [Rozwiązywanie problemów z autorejestracją domeny komputery przyłączone do usługi Azure AD dla klientów niskiego poziomu Windows](troubleshoot-hybrid-join-windows-legacy.md)
 

---

## <a name="azure-ad-register-faq"></a>Rejestrowanie usługi Azure AD — często zadawane pytania

**PYT.: Czy mogę zarejestrować urządzenia BYOD systemu Android lub iOS**

**ODP.:** Tak, ale tylko przy użyciu usługi rejestracji urządzeń na platformie Azure oraz dla klientów hybrydowych. Nie jest obsługiwana przy użyciu usługi rejestracji urządzeń lokalnych w usługach AD FS.

**PYT.: Jak mogę zarejestrować urządzenia z systemem macOS**

**ODP.:** Aby zarejestrować urządzenia z systemem macOS:

1.  [Tworzenie zasad zgodności](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definiowanie zasad dostępu warunkowego dla urządzeń z systemem macOS](../active-directory-conditional-access-azure-portal.md) 

**Uwagi:**

- Użytkownicy, zawarte w zasadach dostępu warunkowego muszą [obsługiwana wersja pakietu Office dla systemu macOS](../conditional-access/technical-reference.md#client-apps-condition) uzyskują dostęp do zasobów. 

- Podczas pierwszej próby dostępu do usługi Użytkownicy są monitowani o zarejestrowanie urządzenia przy użyciu portalu firmy.

---
