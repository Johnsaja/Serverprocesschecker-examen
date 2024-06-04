<#

    Scriptnaam: ServerProcessChecker totaalscript

    Versie: Script voor examenkandidaat

    Functie: Check actieve processen op een server t.o.v. 'whitelist'

    Auteur: Ivo van Pluur / ROC van Twente

    Versie: 1.1

    Datum: Februari 2023



#>



Clear-Host;



# DEFINITIE VARIABELEN # Hiermee wist u de weergave en tekst in het hostprogramma.



#-------------------------------------------------------------------------------

### OPDRACHT VOOR DE EXAMENKANDIDAAT ### OPDRACHT VOOR DE EXAMENKANDIDAAT ###

# Vraag de naam van de server op waarvan je de processen wilt checken en wijs deze toe

# aan de variabele: $server



$server = Read-Host "SVR001"





# deze variabele haalt de datum en tijd op

$datum_tijd = Get-Date;

# Haal de huidige datum en tijd op

$datetime = Get-Date



# Toon de datum en tijd

Write-Output "De huidige datum en tijd is: $datetime"



 # deze variabele haalt de 'whitelist' op

$white_list = "$home\MonTool\whitelist.txt";



# Stel het pad naar het whitelist-bestand in

$home = [System.Environment]::GetFolderPath('UserProfile')

$white_list = "$home\MonTool\whitelist.txt"



# Control



# Deze variabele haalt het bestand met signaleringen op

$signaleringen = "$home\MonTool\signaleringen.txt";



#-------------------------------------------------------------------------------

### OPDRACHT VOOR DE EXAMENKANDIDAAT ### OPDRACHT VOOR DE EXAMENKANDIDAAT ###

# Neem de 'whitelist' in een variabele op met de naam: [string[]]$array_white_list



[array]$array_white_list = Get-Content -Path $white_list

#-------------------------------------------------------------------------------





# de variabele $error wordt leeggemaakt

$Error.Clear();



# weergave startboodschap op het scherm met daarna een wachttijd van 5 seconden

Write-Host "De ServerProcessChecker is gestart op: '$datum_tijd'";

Write-Host "De opgegeven server is: '$server'";

Write-Host "Er wordt gecheckt of server '$server' bestaat.";

Start-Sleep -Seconds 5;



# als de opgegeven server bestaat, neem je van deze server de actieve processen op in een array

# als de opgegeven server NIET bestaat, stopt het script en geef je een melding/warning weer.

# een en ander wordt via de try/catch techniek vormgegeven.

try{

    [string[]]$actieve_processen = Invoke-Command -ComputerName $server -ScriptBlock {Get-Process | `

    Select-Object -ExpandProperty name} -ErrorAction Stop;



    #-------------------------------------------------------------------------------

    ### OPDRACHT VOOR DE EXAMENKANDIDAAT ### OPDRACHT VOOR DE EXAMENKANDIDAAT ###

    # Maak het signaleringenbestand leeg en vul met de naam van de te monitoren server en datum/tijd.

    # Server: <servernaam>

    # Datum/Tijd: <datum en tijd>

"Server: $server" > $signaleringen

    "Datum/Tijd: $datum_tijd" >> $signaleringen





    #-------------------------------------------------------------------------------



    foreach ($proces_naam in $actieve_processen)

    {

        # check op actieve processen die niet in de 'whitelist' voorkomen

        if (-not($proces_naam -in $array_white_list))

        {

            # wil je het proces toevoegen aan de whitelist?

            # Zo nee, neem de procesnaam dan op in het bestand met signaleringen

            $antwoord = Read-Host -Prompt  "'$proces_naam' zit niet in de whitelist. Wil je deze toevoegen aan de whitelist? (j/n)";

            # er is géén afhandeling van foute invoer ingebouwd.

            if ($antwoord.toupper() -eq 'J') # Indien 'j' voeg procesnaam toe aan de whitelist

            {

                $array_white_list += $proces_naam

            }

            else

            {

            #-------------------------------------------------------------------------------

            ### OPDRACHT VOOR DE EXAMENKANDIDAAT ### OPDRACHT VOOR DE EXAMENKANDIDAAT ###

            # Voeg de procesnaam toe aan het signaleringenbestand

		 $proces_naam >> $signaleringen





            #-------------------------------------------------------------------------------

            }

        }



    }



    # wil je het bestand met signalering op het scherm tonen?

    $antwoord_printen = Read-Host -Prompt ('Wil je het bestand met signaleringen op het scherm weergeven? (j/n)')

    if ($antwoord_printen.ToUpper() -eq 'J')

    {

        Get-Content -Path $signaleringen

    }

}

catch{

    # genereer een melding/warning als de opgegeven servernaam onbekend is

    Write-Warning 'De opgegeven servernaam is niet bekend;het script wordt gestopt.';

    break;

}



#-------------------------------------------------------------------------------

### OPDRACHT VOOR DE EXAMENKANDIDAAT ### OPDRACHT VOOR DE EXAMENKANDIDAAT ###

# Genereer het bestand met de whitelist opnieuw (ook al zijn er geen processen aan toegevoegd)



$array_white_list | Out-File -FilePath $white_list

#-------------------------------------------------------------------------------





# geef een afsluitende melding weer op het scherm

Write-Host "Datum/Tijd: $datum_tijd" 

Write-Host  "`nHET SCRIPT IS KLAAR MET DE UITVOERING`nTOT DE VOLGENDE KEER";





# EINDE SCRIPT #
