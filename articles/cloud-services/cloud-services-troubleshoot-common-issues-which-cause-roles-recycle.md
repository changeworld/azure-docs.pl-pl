---
title: Najczęstsze przyczyny recyklingu ról usługi w chmurze | Dokumenty firmy Microsoft
description: Rola usługi w chmurze, która nagle odtwarza może spowodować znaczne przestoje. Oto kilka typowych problemów, które powodują, że role mają być poddane recyklingowi, co może pomóc w skrócenie czasu przestoju.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 554508b1bf784e306cd12a4a601f908e06320933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154973"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Typowe problemy, które powodują odtwarzanie ról
W tym artykule omówiono niektóre z typowych przyczyn problemów z wdrażaniem i zawiera wskazówki dotyczące rozwiązywania problemów, które pomogą Ci rozwiązać te problemy. Wskazanie, że problem istnieje z aplikacją jest, gdy wystąpienie roli nie można uruchomić lub cykli między inicjowania, zajęty i zatrzymania stanów.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Brak zależności środowiska uruchomieniowego
Jeśli rola w aplikacji opiera się na dowolnym zestawie, który nie jest częścią .NET Framework lub biblioteki zarządzanej platformy Azure, należy jawnie dołączyć tego zestawu w pakiecie aplikacji. Należy pamiętać, że inne struktury firmy Microsoft nie są domyślnie dostępne na platformie Azure. Jeśli twoja rola opiera się na takiej ramach, należy dodać te zestawy do pakietu aplikacji.

Przed utworzeniem i spakowaniem aplikacji należy sprawdzić, co następuje:

* Jeśli używasz programu Visual Studio, upewnij się, że **copy local** właściwość jest ustawiona na **True** dla każdego zestawu, do którego istnieje odwołanie w projekcie, który nie jest częścią zestawu Azure SDK lub .NET Framework.
* Upewnij się, że plik web.config nie odwołuje się do żadnych nieużywanych zestawów w elemencie kompilacji.
* **Akcja kompilacji** każdego pliku cshtml jest **ustawiona**na Zawartość . Gwarantuje to, że pliki będą wyświetlane poprawnie w pakiecie i umożliwia wyświetlanie innych plików, do których istnieje odwołanie w pakiecie.

## <a name="assembly-targets-wrong-platform"></a>Montaż jest przeznaczony dla niewłaściwej platformy
Platforma Azure jest środowiskiem 64-bitowym. W związku z tym zestawy .NET skompilowane dla 32-bitowego obiektu docelowego nie będą działać na platformie Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rola zgłasza nieobsługiwanie wyjątków podczas inicjowania lub zatrzymywania
Wszelkie wyjątki, które są generowane przez metody [RoleEntryPoint] klasy, która obejmuje [OnStart], [OnStop]i [Uruchom] metody, są nieobsługiwał wyjątków. Jeśli nieobsługiwał wyjątek występuje w jednej z tych metod, rola zostanie odtworzona. Jeśli rola jest odtwarzanie wielokrotnie, może być zgłaszanie nieobsługiwanie wyjątek za każdym razem, gdy próbuje uruchomić.

## <a name="role-returns-from-run-method"></a>Rola zwraca z run metody
[Run] Metoda jest przeznaczona do uruchamiania przez czas nieokreślony. Jeśli kod zastępuje [Run] metody, należy spać przez czas nieokreślony. Jeśli [Uruchom] metoda zwraca, rola jest odtwarzana.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Niepoprawne ustawienie DiagnosticsConnectionString
Jeśli aplikacja korzysta z usługi Azure Diagnostics, `DiagnosticsConnectionString` plik konfiguracji usługi musi określić ustawienie konfiguracji. To ustawienie powinno określać połączenie HTTPS z kontem magazynu na platformie Azure.

Aby upewnić `DiagnosticsConnectionString` się, że ustawienie jest poprawne przed wdrożeniem pakietu aplikacji na platformie Azure, sprawdź następujące kwestie:  

* Ustawienie `DiagnosticsConnectionString` wskazuje na prawidłowe konto magazynu na platformie Azure.  
  Domyślnie to ustawienie wskazuje na konto emulowanego magazynu, więc przed wdrożeniem pakietu aplikacji należy jawnie zmienić to ustawienie. Jeśli to ustawienie nie zostanie zmienić, wyjątek zostanie zgłoszony, gdy wystąpienie roli podejmie próbę uruchomienia monitora diagnostycznego. Może to spowodować, że wystąpienie roli do odtwolania w nieskończoność.
* Parametry połączenia są określone w następującym [formacie](../storage/common/storage-configure-connection-string.md). (Protokół musi być określony jako HTTPS). Zastąp *MyAccountName* nazwą konta magazynu, a *MyAccountKey* kluczem dostępu:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Jeśli tworzysz aplikację przy użyciu narzędzi platformy Azure dla programu Microsoft Visual Studio, można użyć stron właściwości, aby ustawić tę wartość.

## <a name="exported-certificate-does-not-include-private-key"></a>Wyeksportowany certyfikat nie zawiera klucza prywatnego
Aby uruchomić rolę sieci Web w obszarze SSL, należy upewnić się, że eksportowany certyfikat zarządzania zawiera klucz prywatny. Jeśli do wyeksportowania certyfikatu jest używany *Menedżer certyfikatów systemu Windows,* wybierz opcję **Tak** dla opcji **Eksportuj klucz prywatny.** Certyfikat musi być eksportowany w formacie PFX, który jest jedynym formatem aktualnie obsługiwanym.

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej [artykułów dotyczących rozwiązywania problemów](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) z usługami w chmurze.

Zobacz więcej scenariuszy recyklingu ról w [serii blogów Kevin Williamson.View](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)more role recycling scenarios at Kevin Williamson's blog series .

[Program RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[Onstart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[Onstop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
