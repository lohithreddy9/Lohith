#include<stdio.h>
#include<string.h>
#include<stdlib.h>
#include<sys/types.h>
#include<sys/stat.h>
#include<fcntl.h>
 
int main( int argc, char* arg[] ) 
{
    int fdone[2];
    pid_t childid;
 
    char readBuff[50];
    char writeBuff[50];
    int readCounter;
 
    pipe( fdone );
 
    if( argc < 3 ) 
    {
        printf( "Atleast need two parameters " );
        exit(1);
    }
 
    int fileOpen = open( arg[1], 0 );
    int targetFile = open( arg[2], 0666 );
     
    if ( fileOpen == -1 || targetFile == -1 ) 
    {
        printf( "Opening file failed " );
        exit(1);
    }
    childid = fork();
 
    if( childid == 0 ) 
    {
        close( fdone[1] );
        read( fdone[0], readBuff, sizeof( readBuff ) );
        printf( "The recived string is : %s", readBuff );
        
        write( targetFile, readBuff, strlen( readBuff ) + 1 );
    } 
    else
    {
        
        close( fdone[0] );
         
        while( (readCounter = read( fileOpen, readBuff, sizeof( readBuff ) ) > 0 ) )  
        {
        write( fdone[1], readBuff, sizeof( readBuff ) );
        }
        close( fdone[1] );
    }
}
