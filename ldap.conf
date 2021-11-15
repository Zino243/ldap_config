#!/bin/bash

#Obtener usuarios con uidNumber >= 1000

grep "x:[1-9][0-9][0-9][0-9]:" /etc/passwd > tmp.txt

#Crear o reiniciar archivo ldif
>tmp.ldif

#Recorrer el archivo tmp.txt con la lista de usuarios
while read linea
do
   #Mostrar la línea que vamos a procesar
   echo "$linea"

   #Obtener datos 
   uid=$(echo $linea | cut -d: -f1) # El primer campo, separando con :
   nomComp=$(echo $linea | cut -d: -f5 | cut -d, -f1) # Lo que hay antes de la 1ª coma del 5º campo
   nomArray=($nomComp) #Convierte el nombre en un array de palabras
   nom=${nomArray[0]}
   if [ "$nom" == "" ]
   then
        nom=$uid
   fi
   ape=${nomArray[1]}
   if [ "$ape" == "" ]
   then
        ape=$uid
   fi
   inic=$(echo $nom | cut -c 1)$(echo $ape | cut -c 1)
   uidNum=$(echo $linea | cut -d: -f3)
   usrPass=$(grep $uid: /etc/shadow | cut -d: -f2)
   shell=$(echo $linea | cut -d: -f7)
   homedir=$(echo $linea | cut -d: -f6)

   #Volcar datos al archivo ldif
   echo "dn: uid=$uid,ou=usuarios,dc=somebooks,dc=local" >> tmp.ldif
   echo "objectClass: inetOrgPerson" >> tmp.ldif
   echo "objectClass: posixAccount" >> tmp.ldif
   echo "objectClass: shadowAccount" >> tmp.ldif
   echo "uid: $uid" >> tmp.ldif
   echo "sn: $ape" >> tmp.ldif
   echo "givenName: $nom" >> tmp.ldif
   echo "cn: $nomComp" >> tmp.ldif
   echo "displayName: $nomComp" >> tmp.ldif
   echo "uidNumber: $uidNum" >> tmp.ldif
   echo "gidNumber: 10000" >> tmp.ldif
   echo "userPassword: $usrPass" >> tmp.ldif
   echo "gecos: $nomComp" >> tmp.ldif
   echo "loginShell: $shell" >> tmp.ldif
   echo "homeDirectory: $homedir" >> tmp.ldif
   echo "shadowExpire: -1" >> tmp.ldif
   echo "shadowFlag: 0" >> tmp.ldif
   echo "shadowWarning: 7" >> tmp.ldif
   echo "shadowMin: 8" >> tmdddp.ldif
   echo "shadowMax: 999999" >> tmp.ldif
   echo "shadowLastChange: 10877" >> tmp.ldif
   echo "mail: ${nom[0]}.${nom[1]}@somebooks.com" >> tmp.ldif
   echo "postalCode: 29000" >> tmp.ldif
   echo "o: somebooks" >> tmp.ldif
   echo "initials: $inic" >> tmp.ldif
   echo >> tmp.ldif
done < tmp.txt

#Añadimos los nuevos usuarios a LDAP
ldapadd -x -D cn=admin,dc=somebooks,dc=local -W -f tmp.ldif
rm tmp.txt
rm tmp.ldif
