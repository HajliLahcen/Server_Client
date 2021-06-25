# Server_Client//client
#include <stdio.h>
#include <stdlib.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <netdb.h>
#include <fcntl.h>
#include<string.h> 
#define max 3000
 
 
#define SERV "127.0.0.1"
#define PORT 12345
 
 int main()
{ 
 int port,s,a;
 char reception[max]; 
 FILE *fichier;  
       
  struct  sockaddr_in     serveur;
  struct  hostent         *adr_h;
 
  port = PORT;
  adr_h = gethostbyname(SERV);
  if (!adr_h){fprintf(stderr, "ProblÃ¨me serveur \"%s\"\n",SERV); exit(1);}
 
  s = socket(AF_INET, SOCK_STREAM, 0);
  bzero(&serveur, sizeof(serveur));
  serveur.sin_family = AF_INET;
  bcopy(adr_h->h_addr, &serveur.sin_addr,adr_h->h_length);
  serveur.sin_port = htons(port);
 
printf("**Une demande de connexion est envoye**\n");
 
if(connect(s,(struct sockaddr*)&serveur,sizeof(serveur))<0)
   {perror("**Connexion impossible**");exit(1);}
    printf("**Vous etes connectÃ© au serveur** \n");
    fichier=fopen("/root/Downloads/Projet2021out.txt","w");
do
{                   
a=recv(s,reception,max,0); 
if(a<0) 
{
printf("**Une erreur est survenue lors de la lecture du fichier**!!!!!");
fclose(fichier);
exit(1);
}
				            
if (a == 0) 
break;   
				            
fputs(reception,fichier) ;
}while(strcmp(reception,"Termine")!=0); 	


    printf("**Fin de session**\n");
    close(s) ;
    return 0 ;
} 
===================================================
//serveur
#include <stdio.h>
#include <stdlib.h>
#include <netinet/in.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <errno.h>
#include<netdb.h>
#include <strings.h>
#define max 3000
 
#define PORT 12345
 
int main()
{ int  lg,s,b,c,r,l,t;
   
   struct sockaddr_in serveur;
   struct sockaddr_in client;
   char envoi[max] ,d ;
   struct hostent *adr_h;
   FILE *fichier; 
       
   bzero(&serveur, sizeof(serveur));
   serveur.sin_family = AF_INET;
   serveur.sin_port = htons(PORT);
   serveur.sin_addr.s_addr = INADDR_ANY;
   bzero(&(serveur.sin_zero), 8);
   lg = sizeof(struct sockaddr_in);
 
   if((s=socket(AF_INET, SOCK_STREAM, 0))==-1) {perror("socket");exit(1);}
   
   if(bind(s,(struct sockaddr*)&serveur,sizeof(struct sockaddr))==-1){perror("bind");exit(1);}
 
   if(listen(s,5)==-1){perror("listen");exit(1);}

   printf("**Le serveur est en ecoute**\n");
   
   if((c=accept(s,(struct sockaddr*)&client,&lg))==-1){perror("accept" );exit(1);}

   printf("**Le client est connecte** \n" );

   adr_h=gethostbyname("127.0.0.1");
   bcopy((char *)adr_h->h_addr,(char *)&serveur.sin_addr,adr_h->h_length); 
printf("Voudriez-vous valider l'envoi ? Y/N:  \n");
scanf("%c",&d);
if(d!='Y') exit(1);

fichier=fopen("/root/Desktop/Server/Projet2021.txt","r");
if(fichier==NULL) printf("lll");


else{


while(fgets(envoi,max, fichier)!=NULL) 
{
t=send(c,envoi,max,0); 
if(t<0){
printf("**Une erreur est survenue lors de la transmission du fichier**!!!!");
fclose(fichier);}

send(c,"Termine\0",9,0);    //  fin de la tarnsmission 
printf("\n Transmission complete.\n");
exit(1);
                      
}
printf("**Une erreur est survenue lors de la lecture du fichier**!!!!");
fclose(fichier);
exit(1); }	

   close(c);
   
  return 0;
} 
