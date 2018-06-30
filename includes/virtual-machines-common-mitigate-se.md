---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2018
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: b31e5cc3f99bdbb45aae6f9d71efdabdcc60f9c8
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37138181"
---
**Dokument ostatniej aktualizacji**: 21 maja 2018 3:00 PM PST.

Ostatnie ujawnienie [nowa klasa luk w zabezpieczeniach Procesora](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) znany jako rozważana wykonywania ataków kanału po stronie spowodowało pytania, korzystając z wyszukiwania jasności więcej klientów.  

Microsoft zostało wdrożone środki zaradcze w naszej usługi w chmurze. Infrastrukturę, która uruchamia Azure i izoluje obciążeń klientów od siebie jest chroniony.  Oznacza to, że innych klientów działających na platformie Azure nie ataki aplikację za pomocą te luki w zabezpieczeniach.

Ponadto Azure rozszerza się użycie [pamięci, zachowując konserwacji](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#memory-preserving-maintenance) Jeśli to możliwe, wstrzymanie maszyny Wirtualnej przez 30 sekund, podczas gdy host jest aktualizowany lub maszyna wirtualna zostanie przeniesiona do hosta już zaktualizowany.  Zachowywanie dalsze konserwacji pamięci minimalizuje klientów i eliminuje konieczność ponownego uruchomienia.  Azure będzie korzystać z tych metod podczas wprowadzania aktualizacji całym systemie hosta.

> [!NOTE] 
21 maja 2018 Zero projektu Google i Microsoft poinformowała nową podklasę luki w zabezpieczeniach kanału po stronie rozważana wykonanie znany jako rozważana obejścia magazynu. Bardziej dodatkowe środki zaradcze głębi zostały wdrożone w ramach infrastruktury chmury firmy Microsoft, którego bezpośrednio dotyczy luk rozważana wykonywania. Więcej informacji znajduje się w tym miejscu: https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180012 
>
> W późne 2018 lutego, Intel Corporation opublikowane zaktualizowane [wskazówki poprawki mikrokodu](https://newsroom.intel.com/wp-content/uploads/sites/11/2018/03/microcode-update-guidance.pdf) stan ich wersje mikrokodu, które poprawić stabilność i ograniczyć ostatnie luk w zabezpieczeniach ujawnione przez [Projektu Google Zero](https://googleprojectzero.blogspot.com/2018/01/reading-privileged-memory-with-side.html). Środki zaradcze wprowadzone przez Azure [3 stycznia 2018](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/) nie dotyczy aktualizacji mikrokodu firmy Intel. Microsoft już wprowadzić Silne środki zaradcze ochrony klientów platformy Azure z innych maszyn wirtualnych platformy Azure.  
>
> Mikrokodu firmy Intel adresów variant 2 Spectre - [CVE-2017-5715](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-5715) lub gałęzi docelowej iniekcji — ochronę przed atakami, które mogłyby tylko odpowiednie realizującym obciążeń udostępnionego lub niezaufanych wewnątrz maszyn wirtualnych na platformie Azure. Nasze inżynierów testowania stabilności, aby zminimalizować wpływ na wydajność mikrokodu, przed udostępnieniem jej klientów platformy Azure.  Bardzo kilku klientów Uruchom niezaufanych obciążeń w ramach ich maszyn wirtualnych, większość klientów nie będzie konieczne włączenie tej możliwości wydawane raz. 
>
> Ta strona zostanie zaktualizowany, jako informacje są dostępne.  






## <a name="keeping-your-operating-systems-up-to-date"></a>Aktualizowanie przez systemy operacyjne

Podczas aktualizacji systemu operacyjnego nie jest wymagany do izolowania aplikacji działających na platformie Azure z innych klientów działających na platformie Azure, zawsze jest najlepszym rozwiązaniem w celu zapewnienia aktualności Twojej wersji systemu operacyjnego. Stycznia 2018 i nowsze [pakiety zbiorcze zabezpieczeń dla systemu Windows](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) zawierają środki zaradcze dla te luki w zabezpieczeniach.

W poniższych ofertach Oto nasze zalecane działania, aby zaktualizować System operacyjny: 

<table>
<tr>
<th>Oferty</th> <th>Zalecana akcja </th>
</tr>
<tr>
<td>usług Azure Cloud Services </td>  <td>Włącz Aktualizacje automatyczne lub upewnij się, że używasz najnowszej systemu operacyjnego gościa.</td>
</tr>
<tr>
<td>Azure Linux Virtual Machines</td> <td>Zainstaluj aktualizacje z dostawcą systemu operacyjnego, jeśli jest dostępna. </td>
</tr>
<tr>
<td>Maszyny wirtualne systemu Windows Azure </td> <td>Sprawdź, czy używasz obsługiwanej aplikacji oprogramowania antywirusowego przed zainstalowaniem aktualizacji systemu operacyjnego. Skontaktuj się z dostawcą oprogramowania antywirusowego, aby uzyskać informacje o zgodności.<p> Zainstaluj [zbiorczy zabezpieczeń stycznia](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002). </p></td>
</tr>
<tr>
<td>Inne usługi Azure PaaS</td> <td>Brak akcji wymagane dla klientów korzystających z tych usług. Azure automatycznie aktualizuje Twojej wersji systemu operacyjnego aktualne. </td>
</tr>
</table>

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Dodatkowe wskazówki, jeśli używasz kodzie niezaufanym 

Uruchomić kod z niezaufanej nie jest potrzebne nie działanie dodatkowe klienta. Jeśli zezwolisz na kod, który nie masz zaufania (na przykład można zezwolić jednemu z klientami w celu przekazania pliku binarnego lub fragment kodu, który następnie wykonaj w chmurze w aplikacji), a następnie należy wykonać następujące dodatkowe czynności.  


### <a name="windows"></a>Windows 
Jeśli używasz systemu Windows i obsługujący kodzie niezaufanym, należy również włączyć funkcję Windows polu Przesłanianie wirtualnych adresów jądra (KVA), co zapewnia dodatkową ochronę przed lukami kanału po stronie rozważana wykonywania (specjalnie z myślą o Wariant 3 Meltdown, [CVE-2017-5754](https://www.cve.mitre.org/cgi-bin/cvename.cgi?name=2017-5754), lub ładowania pamięci podręcznej danych nieautoryzowanych). Ta funkcja jest domyślnie wyłączona i może wpłynąć na wydajność, jeśli jest włączona. Postępuj zgodnie z [systemu Windows Server KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) instrukcje dotyczące włączania ochrony na serwerze. Jeśli korzystasz z usług Azure Cloud Services, sprawdź, czy korzystasz z WA-GOŚCIA-systemu operacyjnego — 5.15_201801-01 lub WA-GOŚCIA — systemu operacyjnego — 4.50_201801-01 (dostępne począwszy od na 10 stycznia 2018) i Włącz rejestru klucza za pomocą zadania uruchamiania.


### <a name="linux"></a>Linux
Jeśli korzysta z systemu Linux i hosting kodzie niezaufanym, należy również zaktualizować Linux do nowszej wersji, która implementuje izolacja tabeli stron jądra (KPTI), która oddziela tabele stron używane przez jądro z elementów należących do przestrzeni. Czynniki te wymagają aktualizacji systemu operacyjnego Linux oraz można uzyskać od dostawcy dystrybucji, jeśli jest dostępna. Dostawca systemu operacyjnego można stwierdzić, czy zabezpieczenia są włączone lub wyłączone domyślnie.



## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej, zobacz [Zabezpieczanie klientów systemu Azure z usterka Procesora](https://azure.microsoft.com/blog/securing-azure-customers-from-cpu-vulnerability/).
