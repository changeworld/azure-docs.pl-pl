---
title: Typowe przyczyny odtwarzanie ról usługi w chmurze | Dokumentacja firmy Microsoft
description: Rola usługi chmury, która nagle odtwarzana może spowodować znaczne przestoju. Poniżej przedstawiono niektóre typowe problemy, powodujące ról do odtwarzania, które mogą pomóc w zmniejszeniu przestoju.
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: felixwu
editor: ''
tags: top-support-issue
ms.assetid: 533930d1-8035-4402-b16a-cf887b2c4f85
ms.service: cloud-services
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/03/2017
ms.author: v-six
ms.openlocfilehash: 7f513a7d3fe44cbbf06855059c87c10ddceef7c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2017
ms.locfileid: "23984290"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Typowe problemy, które powodują odtwarzanie ról
W tym artykule omówiono niektóre typowe przyczyny problemów z wdrażaniem i wskazówki dotyczące rozwiązywania problemów, aby pomóc w rozwiązaniu tych problemów. Wskazanie, że istnieje problem z aplikacją jest, gdy wystąpienie roli nie można uruchomić lub jego przełączanie między Stanami inicjowania zajęty i zatrzymywanie.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Brak zależności środowiska uruchomieniowego
Jeśli rola w aplikacji opiera się na dowolnym biblioteki zarządzanej zestawu, który nie jest częścią programu .NET Framework lub platformy Azure, należy jawnie przypisać tego zestawu w pakiecie aplikacji. Należy pamiętać, że innych platform firmy Microsoft, nie są dostępne na platformie Azure domyślnie. Jeśli rola użytkownika opiera się na takie struktury, należy dodać te zestawy do pakietu aplikacji.

Przed kompilacji i pakiet aplikacji należy sprawdzić następujące kwestie:

* Jeśli używasz programu Visual studio, upewnij się, **Kopiuj lokalnie** właściwość jest ustawiona na **True** dla każdego przywoływanego zestawu w projekcie, który nie jest częścią zestawu SDK platformy Azure lub programu .NET Framework.
* Upewnij się, że plik web.config nie odwołuje się do żadnych zestawów nieużywane w elemencie kompilacji.
* **Akcja kompilacji** co .cshtml pliku jest ustawiony na wartość **zawartości**. Dzięki temu, że pliki będą wyświetlane poprawnie w pakiecie i umożliwia innych plików mają być widoczne w pakiecie.

## <a name="assembly-targets-wrong-platform"></a>Zestaw obiektów docelowych nieprawidłowej platformie
Platforma Azure jest 64-bitowego środowiska. W związku z tym skompilowany dla elementu docelowego 32-bitowych zestawów platformy .NET nie będzie działać na platformie Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rola zgłasza nieobsługiwanych wyjątków podczas inicjowania lub zatrzymywanie
Wszelkie wyjątki, które są generowane przez metody [RoleEntryPoint] klasy, która obejmuje [OnStart], [OnStop], i [Uruchom] metody, są nieobsługiwane wyjątki. Jeśli wystąpi nieobsługiwany wyjątek w jednej z tych metod, odtwarzana w roli. Jeśli rola jest odtwarzanie wielokrotnie, może być zgłaszanie Wystąpił nieobsługiwany wyjątek w każdym razem, gdy próbuje uruchomić.

## <a name="role-returns-from-run-method"></a>Zwraca roli z metoda Run
[Uruchom] metoda jest przeznaczona do działają przez nieograniczony czas. Jeśli kod zastępuje [Uruchom] metody go uśpienia przez nieograniczony czas. Jeśli [Uruchom] metoda zwróci wartość, odtwarzana roli.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Nieprawidłowe ustawienie DiagnosticsConnectionString
Jeśli aplikacja używa diagnostyki Azure, musisz określić plik konfiguracji usługi `DiagnosticsConnectionString` ustawienia konfiguracji. To ustawienie należy określić połączenia HTTPS z kontem magazynu platformy Azure.

Aby upewnić się, że Twoje `DiagnosticsConnectionString` ustawienia są poprawne, przed wdrożeniem pakietu aplikacji na platformie Azure, sprawdź następujące kwestie:  

* `DiagnosticsConnectionString` Ustawienie punktów na prawidłowe konto magazynu na platformie Azure.  
  Domyślnie to ustawienie wskazuje konta magazynu emulowanej, należy jawnie zmienić to ustawienie przed wdrożeniem pakietu aplikacji. Jeśli nie należy zmieniać tego ustawienia, jest wyjątek podczas próby uruchomienia Monitora diagnostycznego wystąpienia roli. Może to spowodować wystąpienie roli do odtworzenia w nieskończoność.
* Ciąg połączenia jest określona w następujących [format](../storage/common/storage-configure-connection-string.md). (Protokołu, muszą być określone jako HTTPS). Zastąp *MyAccountName* z nazwą konta magazynu i *MyAccountKey* kluczem dostępu:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Jeśli tworzysz aplikację za pomocą narzędzia Azure dla programu Microsoft Visual Studio, można użyć strony właściwości można ustawić tej wartości.

## <a name="exported-certificate-does-not-include-private-key"></a>Wyeksportowany certyfikat nie zawiera klucza prywatnego
Aby uruchomić rolę sieci web w ramach protokołu SSL, pamiętaj, że zarządzania wyeksportowany certyfikat zawiera klucz prywatny. Jeśli używasz *Menedżera certyfikatów systemu Windows* można wyeksportować certyfikat, pamiętaj o wybraniu **tak** dla **Eksportuj klucz prywatny** opcji. Certyfikat musi być eksportowany w formacie PFX, który jest tylko format obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki
Wyświetl więcej [Rozwiązywanie problemów z artykułów](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) dla usług w chmurze.

Wyświetl więcej roli odtwarzania scenariusze, w [serii blogu Kevina Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Uruchom]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
