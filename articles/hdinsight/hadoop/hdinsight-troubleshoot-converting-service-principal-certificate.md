---
title: Konwertowanie zawartości certyfikatu na podstawową wartość-64 — usługa Azure HDInsight
description: Konwertowanie zawartości certyfikatu jednostki usługi na format ciągu zakodowanego według bazy 64 w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894171"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Konwertowanie zawartości głównego certyfikatu usługi na format ciągu zakodowanego w formacie base-64 w programie HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="issue"></a>Problem

Zostanie wyświetlony komunikat o błędzie informujący, że dane wejściowe nie są prawidłowym ciągiem base-64, ponieważ zawierają znak niefliktowy 64, więcej niż dwa znaki dopełnienie lub znak spoza odstępu między znakami dopełniacza.

## <a name="cause"></a>Przyczyna

Podczas korzystania z programu PowerShell lub wdrożenia szablonu platformy Azure do tworzenia klastrów z usługą Data Lake jako magazynu podstawowego lub dodatkowego zawartość certyfikatu jednostki usługi, która ma dostęp do konta magazynu usługi Data Lake, jest w formacie base-64. Nieprawidłowa konwersja zawartości certyfikatu pfx na ciąg zakodowany base-64 może prowadzić do tego błędu.

## <a name="resolution"></a>Rozwiązanie

Po uzyskaniu certyfikatu jednostki usługi w formacie pfx (zobacz [tutaj,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) aby uzyskać przykładowe kroki tworzenia jednostki usługi), użyj następującego polecenia programu PowerShell lub fragmentu kodu C#, aby przekonwertować zawartość certyfikatu na format base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta, łącząc społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj się z [instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
