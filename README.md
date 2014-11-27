Compiler
========

for exam



#include <cstdlib>
#include <iostream>
#include <string.h>
#include <stdlib.h>
#include <stdio.h>

using namespace std;

int table[7][4]={{2,0,0,0},{2,3,5,0},{4,0,0,0},{4,0,5,0},{7,0,0,6},{7,0,0,0},{7,0,0,0}};
string input;
string stack;
char current_char;
char line[100];
int index=0;
int state=1;
int error=0;
bool Lexical_error=false;
bool Syntactic_errors=false;

struct parser{
        char context[10];
};
struct parser parsetable[6][9] = {{"x","+","-","*","/","(",")","N","$"},
    				           	  {"E","x","x","x","x","TR","x","TR","x"},
    				              {"R","+TR","-TR","x","x","x","","x",""},
    				           	  {"T","x","x","x","x","FY","x","FY","x"},
    				              {"Y","","","*FY","/FY","x","","x",""},
   				                  {"F","x","x","x","x","(E)","x","N","x"}};

char token[50];

char getcharI(){
     return input[index++] ;
}

void skipspace(){
int j=0;
char e[100];
strcpy(e,line);
memset(line,0,100);
     for(int i=0;i<strlen(e);i++)
     {
        if(e[i]!=' ')
        {
             line[j]=e[i];
             j++;
        }          
     }
     
}
void changestate(char current_char){
     int character;
     if(current_char>=48&&current_char<=57)
       character=0;
     else if(current_char=='.')
       character=1;
     else if(current_char=='E')
       character=2;
     else if(current_char=='+'||current_char=='-')
       character=3;
     else
       character=-1;
     if(character!=-1&&table[state-1][character]!=0)
       state=table[state-1][character];
     else
     {
       error=1;
       state=99;
       }
}

bool regular(string str)
{
  error=0;
  index=0;
  input="";
  input=str;
  state=1;
  while(true)
    {
      current_char=getcharI();
      if(current_char=='\0')
        break;
      if(error==1)
        break;
      changestate(current_char);
    }
    
    if(state==2||state==4||state==7)
      return true;
    else
    {
      Lexical_error=true;
      return false;
     }
     
}
string laststring(string s)  //把頭砍掉的string 
{
int i=0;
 while(s[i]!='\0')
 {
  s[i]=s[i+1];
  i++; 
  }
 s[i]='\0';     
  return s;
}

void gettoken(){
     
    string str;
    str="";
    memset(token,0,50);
    int i=0,k=0;
     for(int i=0;i<strlen(line);i++) //遇到有+-*/()就開始切字串存入token 
    {
      if(line[i]!=' ')
      {
        if((line[i]=='+'&&line[i-1]!='E')||(line[i]=='-'&&line[i-1]!='E')||line[i]=='*'||line[i]=='/'||line[i]=='('||line[i]==')')    
             {
              token[k++]=line[i];     
              }  
        else
             {
              str+=line[i];    
              if((line[i+1]=='+'&&line[i]!='E')||(line[i+1]=='-'&&line[i]!='E')||line[i+1]=='*'||line[i+1]=='/'||line[i+1]=='('||line[i+1]==')')
                {
                 if(regular(str)==true)
                 {
                  token[k]='N';
                  str="";                  
                  }
                  k++;
                 }
              } 
         }         
    }   
   
    if(str!="\0"&&str!="+"&&str!="-"&&str!="*"&&str!="/"&&str!="("&&str!=")")
    {
        if(regular(str)==true)
        {
          token[k]='N';
          str="";                  
         }
    }
    for(int u=0;u<=k;u++)
     cout<<token[u];
    token[k+1]='$';
    cout<<endl;   
}

int parser(string tokens)
{
     
     int i=0,terminate,nonterminate,a;
     string stack;
     stack="";
     stack+="E";
     while(1)//tokens[i]!='\0')
     {
      
      if(tokens[i]=='+')terminate=1;
      else if(tokens[i]=='-')terminate=2;
      else if(tokens[i]=='*')terminate=3;
      else if(tokens[i]=='/')terminate=4;
      else if(tokens[i]=='(')terminate=5;
      else if(tokens[i]==')')terminate=6;
      else if(tokens[i]=='N')terminate=7;
      else if(tokens[i]=='$')terminate=8;
      //cout<<"now tokens:"<<tokens[i]<<endl;
      if(stack[0]=='E')nonterminate=1;
      else if(stack[0]=='R')nonterminate=2;
      else if(stack[0]=='T')nonterminate=3;
      else if(stack[0]=='Y')nonterminate=4;
      else if(stack[0]=='F')nonterminate=5;
      
       stack = parsetable[nonterminate][terminate].context+laststring(stack);
       cout<<"now stack is: "<<stack<<endl;    
      if(stack[0]==token[i])
      {
       i++;
       stack = laststring(stack);
       // cout<<"前面一樣 削掉 現在stack:"<<stack<<endl; 
       // cout<<"消校後 tokens:"<<tokens[i]<<endl;
       }
       if(stack[0]=='\0'&&token[i]=='$')
       {
       return 1;
       break;
       }
       if(stack[0]=='x'||(stack[0]=='\0'&&token[i]!='\0'))
       {
        Syntactic_errors=true;
        cout<<"Syntactic errors"<<endl;
        return 0;
        break;
        }
       
      }
      
}
int main(int argc, char *argv[])
{
  gets(line);
  skipspace();
  while(strcmp(line,"exit"))
  {
    cout<<line<<endl;
    gettoken();
    if(Lexical_error==true)
      cout<<"Lexical error"<<endl;
    else
      {           
       if(parser(token))
         cout<<"success"<<endl;         
       }
     cout<<"please input"<<endl;
     memset(line,0,100);
     gets(line);
     skipspace();
   }   
    system("PAUSE");
    return EXIT_SUCCESS;
}

