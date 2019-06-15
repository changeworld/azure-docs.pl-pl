---
title: Typowe przyczyny powtarzania cykli ról usługi w chmurze | Dokumentacja firmy Microsoft
description: Rola usługi w chmurze nagle odtwarzania może być przyczyną znaczących przestojów. Poniżej przedstawiono niektóre typowe problemy, które powodują recyklingowi, ról, które mogą pomóc ograniczyć przestoje.
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
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 2a9214b918883e493ebe5c93fc7f56e7ce9c77ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60652217"
---
# <a name="common-issues-that-cause-roles-to-recycle"></a>Typowe problemy, które powodują odtwarzanie ról
W tym artykule omówiono niektóre typowe przyczyny problemów z wdrażaniem i wskazówki dotyczące rozwiązywania problemów, aby rozwiązać te problemy. Wskazanie, że istnieje problem z aplikacją jest, gdy wystąpienie roli nie można uruchomić lub jego cykli między Stanami inicjowanie, zajęta i zatrzymywanie.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Brak zależności środowiska uruchomieniowego
Jeśli roli w aplikacji, który opiera się na dowolnym zestawie, który nie jest częścią oprogramowania .NET Framework lub Azure zarządzanej biblioteki, należy jawnie przypisać tego zestawu w pakiecie aplikacji. Należy pamiętać innych platform firmy Microsoft nie są domyślnie dostępne na platformie Azure. Jeśli rola użytkownika opiera się na takie struktury, należy dodać te zestawy do pakietu aplikacji.

Zanim kompilacji i pakietów aplikacji, sprawdź następujące informacje:

* Jeśli używasz programu Visual studio, upewnij się, **Kopiuj lokalnie** właściwość jest ustawiona na **True** dla każdego zestawu odwołania w projekcie, który nie jest częścią zestawu Azure SDK ani programu .NET Framework.
* Upewnij się, że plik web.config nie odwołuje się do wszystkich zestawów nieużywane w elemencie kompilacji.
* **Build Action** .cshtml każdego pliku jest ustawiony na wartość **zawartości**. Gwarantuje to, że pliki zostaną poprawnie wyświetlone w pakiecie i umożliwia innych przywoływanych plików, które pojawią się w pakiecie.

## <a name="assembly-targets-wrong-platform"></a>Zestaw obiektów docelowych nieprawidłowej platformie
Platforma Azure jest 64-bitowego środowiska. W związku z tym zestawów platformy .NET skompilowanych dla elementu docelowego 32-bitowe nie będą działać na platformie Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Rola zgłasza nieobsługiwane wyjątki podczas inicjowania lub zatrzymywania
Wszelkie wyjątki wyrzucane przez metody [RoleEntryPoint] klasy, która obejmuje [OnStart], [OnStop], i [Run] metody, są nieobsługiwane wyjątki. Jeśli nieobsługiwany wyjątek występuje w jednej z następujących metod, będzie odtwarzanie roli. Jeśli roli jest odtwarzane wielokrotnie, może być zgłaszanie Wystąpił nieobsługiwany wyjątek w każdym razem, gdy spróbuje uruchomić.

## <a name="role-returns-from-run-method"></a>Rola wraca z metody uruchamiania
[Run] metoda jest przeznaczona do uruchamiania na czas nieokreślony. Jeśli Twój kod zastępuje [Run] metody jej uśpienia na czas nieokreślony. Jeśli [Run] metoda zwróci wartość, rola jest odtwarzana.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Nieprawidłowe ustawienie DiagnosticsConnectionString
Jeśli aplikacja używa usługi Azure Diagnostics, należy określić plik konfiguracji usługi `DiagnosticsConnectionString` ustawienia konfiguracji. To ustawienie należy określić połączenia HTTPS do swojego konta magazynu na platformie Azure.

Aby upewnić się, że Twoje `DiagnosticsConnectionString` ustawienia jest poprawna, przed wdrożeniem pakietu aplikacji na platformie Azure, sprawdź poniższe:  

* `DiagnosticsConnectionString` Ustawienie punktów na prawidłowe konto magazynu na platformie Azure.  
  Domyślnie to ustawienie wskazuje konta magazynu emulowanej, aby jawnie musi zmienić to ustawienie, przed wdrożeniem pakietu aplikacji. Jeśli nie możesz zmienić to ustawienie, wyjątek jest generowany, gdy wystąpienie roli próbuje uruchomić monitora diagnostycznego. Może to spowodować wystąpienie roli odzyskać na czas nieokreślony.
* Ciąg połączenia jest określona w następującym [format](../storage/common/storage-configure-connection-string.md). (Protokół, muszą być określone jako HTTPS). Zastąp *MyAccountName* nazwą konta magazynu i *MyAccountKey* swoim kluczem dostępu:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Jeśli tworzysz aplikację za pomocą narzędzia Azure Tools dla programu Microsoft Visual Studio, można użyć strony właściwości można ustawić tę wartość.

## <a name="exported-certificate-does-not-include-private-key"></a>Wyeksportowany certyfikat nie zawiera klucza prywatnego
Aby uruchomić rolę sieci web w ramach protokołu SSL, upewnij się, że certyfikat wyeksportowany zarządzania zawiera klucz prywatny. Jeśli używasz *Windows Menedżer certyfikatów* można wyeksportować certyfikatu, należy wybrać **tak** dla **Eksportuj klucz prywatny** opcji. Certyfikat musi być eksportowany w formacie PFX, który jest jedynym formatem, które są obecnie obsługiwane.

## <a name="next-steps"></a>Kolejne kroki
Wyświetl więcej [artykuły dotyczące rozwiązywania problemów](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) dla usług w chmurze.

Wyświetl jedna rola odtwarzanie scenariuszy w [serię wpisów w blogu Kevina Williamson](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Run]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
