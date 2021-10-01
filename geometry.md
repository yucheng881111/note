# 運算幾何
## 向量旋轉
向量左乘旋轉矩陣。須注意正負，負角度旋轉需加負號。

![](https://i.imgur.com/7pEUNGO.png)

## 線段交點
解方程式。數學證明:
![](https://i.imgur.com/MT1AWjZ.png)

程式碼(來源: http://web.ntnu.edu.tw/~algo/Point.html)

![](https://i.imgur.com/cr1MqBy.png)

## 例題
UVa problem 11178: Morley's Theorem
![](https://i.imgur.com/hnIIZkr.png)

題:
將任一三角形ABC的三個角做三等分線，交於DEF且DEF是一個等腰三角形。給定ABC三個點的座標，求出DEF的座標。

解:
先求出角B，接著將向量BC旋轉角B/3度，同理也對CB反方向旋轉角C/3度。求兩個旋轉後向量的交點，即頂點D。用相同手法相繼求出頂點E、F。

```
#include<bits/stdc++.h>

using namespace std;

struct point{
    double x, y;
    point(double X, double Y){
        x = X;
        y = Y;
    }
};

typedef point Vector;

Vector operator +(Vector a, Vector b){
    return Vector(a.x+b.x, a.y+b.y);
}

Vector operator -(Vector a, Vector b){
    return Vector(a.x-b.x, a.y-b.y);
}

Vector operator *(Vector a, double d){
    return Vector(a.x*d, a.y*d);
}

Vector operator /(Vector a, double d){
    return Vector(a.x/d, a.y/d);
}

double dot(Vector a, Vector b){
    return a.x*b.x + a.y*b.y;
}

double cross(Vector a, Vector b){
    return a.x*b.y - a.y*b.x;
}

double len(Vector a){
    return sqrt(a.x*a.x + a.y*a.y);
}

double angle(Vector a, Vector b){
    // 內積/長長
    double cos_theta = dot(a,b)/(len(a)*len(b));
    return acos(cos_theta);
}

Vector Rotate(Vector a, double theta){
    //左乘旋轉矩陣
    return Vector(cos(theta)*a.x-sin(theta)*a.y, sin(theta)*a.x+cos(theta)*a.y);
}

point GetIntersectionPoint(point p1, Vector v1, point p2, Vector v2){
    Vector v3 = p1 - p2;
    return p1 + v1 * (cross(v2,v3)/cross(v1,v2));
}

point GetpointD(point A, point B, point C){
    Vector BA = A - B;
    Vector BC = C - B;
    double LB = angle(BA, BC);
    Vector BC_rotate = Rotate(BC, LB/3);
    Vector CA = A - C;
    Vector CB = B - C;
    double LC = angle(CA, CB);
    Vector CB_rotate = Rotate(CB, -LC/3);

    point D = GetIntersectionPoint(B, BC_rotate, C, CB_rotate);
    return D;
}

int main(){
    int n;
    cin >> n;
    while(n--){
        int x1, y1, x2, y2, x3, y3;
        cin >> x1 >> y1 >> x2 >> y2 >> x3 >> y3;
        point A(x1,y1);
        point B(x2,y2);
        point C(x3,y3);
        point D = GetpointD(A,B,C);
        point E = GetpointD(B,C,A);
        point F = GetpointD(C,A,B);
        printf("%.6lf %.6lf %.6lf %.6lf %.6lf %.6lf\n", D.x, D.y, E.x, E.y, F.x, F.y);
    }


	return 0;
}

```






