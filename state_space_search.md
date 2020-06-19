# State Space Search

![](https://i.imgur.com/bqYeAmN.jpg)
最好配合bitset做狀態壓縮，較容易存進queue或map，也能避免TLE或MLE。



## Sample Problem
### Uva 10422 (string 壓縮狀態)
題目敘述:
騎士走法，全盤面(5x5)只有一個空白格，有12個白騎士和12個黑騎士。求排出題目要求的陣型最少需要的步數。

```
#include<bits/stdc++.h>

using namespace std;
const string finish="111110111100 110000100000";
unordered_map<string,bool> m;
int bfs(string s){
    queue<pair<string,int>> q;
    q.push({s,0});
    m[s]=1;
    while(!q.empty()){
        string temp=q.front().first;
        int num=q.front().second;
        if(num>10){
            break;
        }
        q.pop();
        char board[5][5];
        int p=0;
        int wi,wj;
        for(int i=0;i<5;++i){
            for(int j=0;j<5;++j){
                board[i][j]=temp[p];
                if(temp[p]==' '){
                    wi=i;
                    wj=j;
                }
                p++;
            }
        }
        for(int i=0;i<5;++i){
            for(int j=0;j<5;++j){
                if((abs(wi-i)==1&&abs(wj-j)==2)||(abs(wi-i)==2&&abs(wj-j)==1)){
                    swap(board[wi][wj],board[i][j]);
                    string tmp="";
                    for(int x=0;x<5;++x){
                        for(int y=0;y<5;++y){
                            tmp+=board[x][y];
                        }
                    }
                    if(tmp==finish){
                        return num+1;
                    }
                    if(m[tmp]==0){
                        q.push({tmp,num+1});
                        m[tmp]=1;
                    }
                    swap(board[wi][wj],board[i][j]);
                }
            }
        }
    }
    return 11;
}

int main(){
    int t;
    cin>>t;
    fflush(stdin);
    while(t--){
        m.clear();
        string s="";
        for(int i=0;i<5;++i){
            string tmp;
            getline(cin,tmp);
            s+=tmp;
        }
        if(s==finish){
            cout<<"Solvable in 0 move(s)."<<endl;
            continue;
        }
        int ans=bfs(s);
        if(ans<11){
            cout<<"Solvable in "<<ans<<" move(s)."<<endl;
        }else{
            cout<<"Unsolvable in less than 11 move(s)."<<endl;
        }

    }


return 0;
}

```
## Sample Problem
### NCTUOJ914-Let's Take a Break (bitset 狀態壓縮,雙向BFS)
There is a 5 * 5 game board with each cell is occupied by a soldier. There are four kinds of soldiers: white(W), yellow(Y), purple(P\), and black(B), and there is only one white soldier. Assume the white soldier is at (i,j). In each operation, he can swap the position with the soldier on (i',j') if (|i-i'|, |j-j'|) in {(1,2),(2,1)}
Now Kou wonder the minimum operations need to be performed to change a game board to another one.

也是棋盤問題，跟上題概念差不多
```
#include<bits/stdc++.h>

using namespace std;

string convert(long long t){
    bitset<50> b(t);
    string s="";
    for(int i=48;i>=0;i-=2){
        int tmp=b[i+1]*2+b[i];
        if(tmp==0){
            s+='W';
        }else if(tmp==1){
            s+='Y';
        }else if(tmp==2){
            s+='P';
        }else{
            s+='B';
        }
    }
    return s;
}

long long convert_int(string s){
    string t="";
    for(int i=0;i<25;++i){
        if(s[i]=='W'){
            t+="00";
        }else if(s[i]=='Y'){
            t+="01";
        }else if(s[i]=='P'){
            t+="10";
        }else{
            t+="11";
        }
    }
    bitset<50> b(t);
    return b.to_ullong();
}

char board[5][5];
unordered_map<long long,bool> ms; //start to finish
unordered_map<long long,bool> mf; //finish to start
queue<long long> qs; //start to finish
queue<long long> qf; //finish to start
int step=1;

void solve(){

    while(1){
        // queue start to finish
        int qs_size=qs.size();
        for(int i=0;i<qs_size;++i){
            long long t=qs.front();
            string temp=convert(t);
            qs.pop();
            int p=0;
            int wi,wj;
            for(int k=0;k<5;++k){
                for(int l=0;l<5;++l){
                    board[k][l]=temp[p];
                    if(temp[p]=='W'){
                        wi=k;
                        wj=l;
                    }
                    p++;
                }
            }
            for(int i=0;i<5;++i){
                for(int j=0;j<5;++j){
                    if((abs(wi-i)==1&&abs(wj-j)==2)||(abs(wi-i)==2&&abs(wj-j)==1)){
                        swap(board[i][j],board[wi][wj]);
                        string b_swap="";
                        for(int k=0;k<5;++k){
                            for(int l=0;l<5;++l){
                                b_swap+=board[k][l];
                            }
                        }
                        long long convert_swap=convert_int(b_swap);
                        if(mf[convert_swap]==1){
                            return ;
                        }
                        if(ms[convert_swap]==0){
                            qs.push(convert_swap);
                            ms[convert_swap]=1;
                        }
                        swap(board[i][j],board[wi][wj]);
                    }
                }
            }

        }
        if(step==20){
            return ;
        }
        step++;

        int qf_size=qf.size();
        for(int i=0;i<qf_size;++i){
            long long t=qf.front();
            string temp=convert(t);
            qf.pop();
            int p=0;
            int wi,wj;
            for(int k=0;k<5;++k){
                for(int l=0;l<5;++l){
                    board[k][l]=temp[p];
                    if(temp[p]=='W'){
                        wi=k;
                        wj=l;
                    }
                    p++;
                }
            }
            for(int i=0;i<5;++i){
                for(int j=0;j<5;++j){
                    if((abs(wi-i)==1&&abs(wj-j)==2)||(abs(wi-i)==2&&abs(wj-j)==1)){
                        swap(board[i][j],board[wi][wj]);
                        string b_swap="";
                        for(int k=0;k<5;++k){
                            for(int l=0;l<5;++l){
                                b_swap+=board[k][l];
                            }
                        }
                        long long convert_swap=convert_int(b_swap);
                        if(ms[convert_swap]==1){
                            return ;
                        }
                        if(mf[convert_swap]==0){
                            qf.push(convert_swap);
                            mf[convert_swap]=1;
                        }
                        swap(board[i][j],board[wi][wj]);
                    }
                }
            }

        }
        step++;

    }

}

void clear_q(queue<long long> &q){
    queue<long long> e;
    swap(q,e);
}

int main(){
    int n;
    cin>>n;
    while(n--){
        ms.clear();mf.clear();clear_q(qs);clear_q(qf);
        step=1;
        string start="";
        string finish="";
        char c;
        for(int i=0;i<5;++i){
            for(int j=0;j<5;++j){
                cin>>c;
                start+=c;
            }
        }
        for(int i=0;i<5;++i){
            for(int j=0;j<5;++j){
                cin>>c;
                finish+=c;
            }
        }

        long long start_state=convert_int(start);
        long long finish_state=convert_int(finish);
        ms[start_state]=1;
        mf[finish_state]=1;
        if(start_state==finish_state){
            cout<<"0"<<endl;
            continue;
        }
        qs.push(start_state);
        qf.push(finish_state);
        solve();
        cout<<step<<endl;
    }

return 0;
}
```
