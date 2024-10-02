# PROVA DATA SCIENCE HACKATÓ SALÓ DE LA OCUPACIÓ 

## INTRODUCCIÓ
El conjunt de dades que he triat es el de l’enquesta sobre la bretxa digital a Barcelona. Es un dataset força extens i he descartat les variables que m’han semblat menys interessants per l’anàlisi sobre la bretxa digital. De 135 columnes m’he quedat amb 88 i n’he creat una de nova amb una mena de Scoring puntuant les diferents preguntes sobre l’ús de les TIC i agrupant-les en una sola variable numèrica.
### DEPURACIÓ DE DADES
Després de descartar registres erronis, duplicats comprovo els valors nuls i veig que n’hi ha bastants.
### VALORS NULS
Comprovo doncs la coherència dels valors nuls. A la enquesta hi ha opcions de no sap i no contesta que no son valors nuls, els nuls, doncs, provenen de preguntes condicionades a altres anteriors i que no es poden respondre si s’ha contestat que no a la anterior.
* Els nuls de P1A,Q1A son els que no tenen connexió a casa, si els sumem als que sí en tenen P1B donen el total de la mostra.
* Els 75 de Longitud i Latitud son els que no indiquen on viuen. En cas de voler utilitzar aquestes variables per a algun model o regressió lineal s'haurien d'eliminar.
* Els d'ID-PAIS son els que son espanyols.
* Els P2_5B, els que no disposen d'altres dispositius diferents als especificats a la llar.
* Els de P2_A del que no tenen smartphone
* Els de P2-B dels que si en tenen i tenen també una tarifa de dades.
* Els del grup P3 estan formulades en multi-resposta ja que un mateix enquestat pot tenir correus diferents. Els nuls provenen dels que no tenen correu electrònic, personal, professional o indiferent o no responen a la pregunta no sap (tots) o no contesta.
* Els del grup P5 que son 187, son els que no utilitzen internet per a res. Per tant no poden contestar a moltes de les preguntes de l'enquesta.
* P5A_18_cod els que no han participat en cap moviment o grup.
* Els del grup P14 son els que no tenen dificultats per accedir a internet.
* Els de P2_LLAR son els que no tenen dispositius a casa.
Comprovat això, la gestió de valors nuls ja està feta, no cal eliminar fileres ni realitzar imputació de valors.
### TIPUS DE DADES
Comprovo el tipus de dades. La majoria son int64, alguns float64 i un object. Per optimitzar el model i fer-lo mes consistent i eficient, canvio els tipus de les dades. Gairebé tots podrien ser integer ja que son categories fetes amb nombres enters o l'edat (Q2) que també son enters. 
Creo un bucle amb try except, per veure quines es poden convertir directament i quines no. 
Trio el tipus Int64 ja que permet valors nuls.
Identifico les columnes que no canvien directament:
* Nom_Districte, que la deixarem com a tipus Object, ja que ha de ser text.
* Longitud, Latitud i Pes contenen decimals, així que les deixarem com a float.
### OUTLIERS
Al tractar-se, la majoria, de variants de categoritzades en bins del 0 al 5, no tindrem outliers. Si volguéssim aplicar algun model amb alguna de les numèriques com edat (Q2), convindria visualitzar els possibles outliers amb un boxplot o scatterplot. Ho farem mes endavant.
El que podem considerar es tractar com a outliers les respostes 95, 98 i 99 que corresponen altres respostes, no sap, no contesta i canviar aquests valors per nuls. Això facilitaria la visualització exploratòria per veure la distribució de les variables categòriques. En un context de projecte professional, hauríem de consultar si l'eliminació d'aquestes dades afecta a l'anàlisi, es a dir, quina importància te saber quanta gent a respost 95,98 o 99. De ser necessari també es podrien substituir aquest valors per 6,7 i 8. 
Decideixo veure quin percentatge de respostes amb aquest valors hi ha a les preguntes on son presents.
En la majoria de casos el percentatge no arriba al 2%. Els casos en que sí ho fa son:
* P1A, Q1A i P1B en que es pregunta pel tipus de connexió i per la qualitat d'aquesta. Considero que no es rellevant ja que el fet de no saber-ho o usar tecnologies molt minoritàries no afecta al resultat general, simplement disminueix una mica la mostra.
* P2B en que es demana el motiu pel que no tenen tarifa de dades a internet. Un 10% es força elevat però hem de tenir en compte que es un 10% de la gent que no te tarifa de dades al mòbil, que es un percentatge molt petit del total de la mostra, i a més que el fet que no puguin aportar informació sobre el motiu no desvirtua el resultat de l'enquesta, tan sols merma una mica la mostra en aquesta pregunta.
* P3_98 i P3_99 formen part de una pregunta multi-resposta on es pot contestar mes d'un valor a la mateixa pregunta. En concret en aquestes dues hi van els valors dels que contesten no sap o no contesta a la pregunta de si te email. Podríem suposar que si no ho saben o no contesten vol dir que no en tenen, però només es una suposició. En qualsevol cas eliminar aquestes valors i passar-los a nuls no afecta per a res a la pregunta, ja que no l'estan contestant.
* P5_A18_Cod. Després de preguntar si participa a través d'internet a moviments o grups socials, en aquesta pregunta es demana "Tipus de moviment o grup en el que ha participat". Aquí la opció 5 es altres pel que només hi haurà No sap i No contesta. Un altre cop l'eliminació d'aquests valors no desvirtua el resultat, només en merma una mica la mostra d'aquesta sub-pregunta específica.
* P14_2Cod. Aquesta pregunta s'adreça només a aquells que han contestat que tenen alguna discapacitat que els hi dificulta l'accés a internet i a més han contestat que no disposen d'eines tecnològiques que els ajudin a superar aquesta dificultat. La pregunta concreta es "Per què no disposa de les eines tecnològiques que li permetin superar 
aquestes dificultats per accedir a Internet" i exactament un 12,5% han contestat que no ho saben.
Aprofundeixo en aquesta pregunta i veig que representa una part ínfima de la mostra i, a mes, el fet de no conèixer la resposta no afecta al resultat de la pregunta general. Convertir no sap i no contesta en nul no afectarà al resultat.
Així doncs, veient la poca incidència que tenen en les dades i que en cap cas convertir-los en nuls resta valor ni desvirtua el dataset, decideixo convertir a valors nuls totes les respostes 95, 98 i 99. 
### ESTANDARDITZACIÓ
Al tractar-se, la majoria, de dades categoritzades en respostes del 0 al 5 (tret d'ID4_PAIS), només tenim l'edat (Q2) com a possible dada a normalitzar o estandarditzar, a més aquesta dada també la tenim ja categoritzada (Edat), per tant no serà necessari.
Dono el dataset per filtrat.
### ENGINYERIA DE CARACTERÍSTIQUES: Creació de Variables
Em sembla rellevant poder crear una nova variable que faci de puntuació del nivell de digitalització de cada enquestat, atorgant un valor a cada resposta sobre aquest tema i realitzant una mena d'Scoring de l'ús de les TIC de cada enquestat. Assigno diferents puntuacions a preguntes clau per valorar el nivell de l'enquestat:
* A P1, Assigno 10 punts als enquestats que tenen connexió d'internet a casa i 0 als que no.
* Els del grup P2_ fan referència a la quantitat de dispositius. Cada dispositiu serà un punt.
* P2 i P2A pregunten si tenen mòbil i tarifa de dades, amb resposta positiva, sumo 5 punts per la primera i 10 per la segona.
* Les P3_ les valoro en conjunt: 4 punts per la resposta 1 i 3 per cada resposta 2 o 3, finalment 0 punts pel 0.
* La P4 la valora segons la freqüència d'us de manera inversa al valor contestat.
* De les P5_ trio les que seran puntuades per freqüència de manera inversa.
* Les altres P5_ seran puntuades amb 1 punt per l'1 i 0 pel 2.



## RESULTATS: ANÀLISI EXPLORATÒRI DE DADES (EDA)

### EDAT
Faig un histograma i un boxplot per veure la distribució de la variable numèrica d'edat (Q2) i detectar posibles outliers.


![image](https://github.com/user-attachments/assets/26294517-4bff-4c70-9d6c-2d3aec7805de)

S'aprecia una distribució relativament normal, amb la salvetat que l'edat inicial de la mostra son 16 anys.
Destaquen un pic dels 32 als 35, tot i que, el màxim de la corba de distribució se situa entre els 40 i els 45.


![image](https://github.com/user-attachments/assets/f8d0e1e7-1971-4132-81a4-d909f3d6febd)

S'aprecia una distribució d'edats força simètrica. La mediana se situa als 50 anys, molt propera la mitjana de 50,5.
L'espai entre el Q1 i el Q3 es força ampli, per tant dins les edats centrals hi ha bastanta variabilitat. L'IQR se situa entre els 40 i el 60.
S'aprecia força simetria amb una mica més de concentració cap a les edats mes joves.
Els bigotis van des dels 16 anys fins als 96. No hi ha punts fora dels bigotis, com era de preveure no hi ha valors extrems o outliers.

#### CONCLUSIÓ:
#### Tenim una mostra d'edat molt equilibrada i variada i , per tant, força representativa de la societat.


### SCORING D'ÚS DE LES TIC
Visualitzant la distribució d'Scoring podrem veure, de manera general, quina distribució hi ha de l'ús de les tecnologies de la informació i la comunicació a la mostra.


![image](https://github.com/user-attachments/assets/ee7be847-0e19-4649-88da-91fba9c08242)

Es mostra una distribució amb forma de campana de gauss, es a dir normal, amb bastanta concentració a les puntuacions entre 100 i 125, on se situa el màxim de la corba.
No obstant s'observa un pic important a les puntuacions mes baixes de 0 a 5. Es va suavitzant de 5 a 30 punts on comença la corba principal. Es podria parlar d'una lleugera estructura bimodal.
Es força simètrica amb una lleugera asimetria cap a les puntuacions mes baixes.

#### CONCLUSIÓ:
#### La asimetria cap a les puntuacions baixes i sobretot el pic en les puntuacions mínimes posen de manifest que sí que hi ha una bretxa digital, es a dir una diferència pronunciada entre l'ús dels usuaris intensius de les TIC i els que gairebé no les utilitzen.


![image](https://github.com/user-attachments/assets/1b53aae2-2d84-4947-88de-3b1b9ac055bb)

Observem que el rang entre Q2 i Q3 es força estret, la majoria de distribució es concentra en un interval de puntuacions estret. 
Pel que fa a la simetria la mediana esta lleugerament a la dreta de la caixa entre Q2 i Q3, per tant hi ha una major concentració de dades a l'esquerra amb una menor puntucaió.

#### CONCLUSIÓ:
#### Podem veure com hi ha molts valors per fora els bigotis, concretament abans del Q1. Tants que, mes que considerarlos com a outliers i descartar-los, hem de pensar que això posa de manifest la existencia de la Bretxa digital.


### SCORING Vs EDAT
Comparant aquestes dues variables, podrem veure si hi ha una relació entre edat i nivell d'ús de les TIC. 
Faig un Scatterplot amb línia de regressió lineal.


![image](https://github.com/user-attachments/assets/3b371024-0ee0-4042-8b6f-c3485f991834)

Veiem que hi ha una clara relació entre les dues variables: a mes edat, menys puntuació.
Tot i que la línia de la regressió lineal indica una relació lineal decreixent o negativa, sembla clar que la relació no segueix un patró lineal sinó mes aviat parabòlic. Per això podríem parlar de relació no lineal quadràtica. 
Això indica que podria ser interessant aplicar algun altre model mes complex per comparar aquestes variables. De ser així potser caldria estandarditzar o normalitzar les dues variables.
També veiem un sector molt menys densament poblat entre les puntuacions menors de 50 punts i les properes a 0 que tornen a ser mes abundants. Això també ens dona una visualització de l'existència de la bretxa digital.

#### CONCLUSIÓ:
#### La relació quadràtica inversa existent entre Scoring i Edat, ens indica que l'edat es un factor decisiu en el nivell d'ús de les TIC dels enquestats.
#### El vuit existent entre les puntuacions mes baixes i les properes a 0 suggereixen l'existència real d'una bretxa digital.



## CONCLUSIONS
### Hem pogut demostrar la relació entre edat i l’ús de les TIC i visualitzar la bretxa digital de manera clara, constatant que es una realitat.
