# 计算几何板子


#### 二维几何（未完善）

```cpp
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <cmath>
#include <vector>

/**
 * 本板子属于半成品，有些功能并没有验证
 * 函数说明：
 *          关于点的函数
 * 点的Point(double, double) 构造函数
 * + 向量加法
 * - 向量减法
 * == 判断两个点是否相等
 * *(Point) 向量点乘
 * *(double) 向量伸长(没有除法，要用除法直接乘倒数)
 * ^ 向量叉乘
 * < 点对点的比较
 * double len() 向量的长度，也可以用来求两个点的距离
 * Point rotate(double angle)  向量逆时针旋转angle弧度
 * Point rotate(Point, double)  点让点p逆时针旋转angle弧度
 * void print() 将点输出
 * int init() 输入点的坐标 返回值和scanf相同
 *          其他非结构体函数
 * angle(Point&, Point&) 计算两个向量的夹角
 * 
 *          关于线的函数
 * Line(Point, Point) 构造函数
 * Line(Point, double) 根据一个点和一个倾斜角 0 <= angle < PI确定直线 （未验证）
 * double len() 返回线段的长度
 * double point(double t)  返回距离点p向前t倍向量的点
 * double angle() 返回直线的倾斜角 范围[0, PI) (未验证)
 * double disPointToLine(const Point&) 点到这条直线的距离
 * double disPointToSeg(const Point&) 点到这条线段的距离（未验证）
 * Point getPro(const Point&) 点在这条线上的投影 （未验证）
 * Point getSym(const Point&) 点关于这条线的对称点 （未验证）
 * bool isOnLine(const Point&) 验证该点是否在这条直线上（未验证）
 * bool isOnSeg(const Point&) 验证该点是否在这条线段上（未验证）
 * Point cross(Line&)  直线和这条直线的交点，前提是相交
 * void print()  输出这条线段
 * 
 *          其他非结构体
 * int LineAndLine(Line&, Line&)  直线和直线的关系 0平行 1重合 2相交 （未验证）
 * Point getLineInter(const Line&, const Line&) 求两直线的交点,必须相交才能调用 （此函数有问题）
 * int SegAndSeg(const Line& l1, const Line& l2) 两个线段的关系 0不相交 1非规范相交(其中一个线段的端点和另一个线段相交) 2规范相交 （未验证）
 * int LineAndSeg(const Line& line, const Line& seg) 直线和线段的关系，0不相交 1非规范相交 2规范相交 （未验证）
 * 
 *           关于圆的函数
 * Circle(Point, double) 构造函数
 * Circle(Point, Point, Point) 过三点的圆
 * double area() const; 返回圆的面积
 * double circum() const  返回圆的周长
 * int PointAndCircle(Point&) 点和圆的关系 返回 0圆外 1圆上 2圆内
 * int LineAndCircle(Point&)  点和圆的关系 返回 0不相交 1相交 2相交两个点（未验证）
 * int CircleAndCircle(Circle&) 圆和圆的关系 返回 0内含 1内切 2相交两点 3外切 4外离 （未验证）
 * 
 *         关于三角形的函数
 * Triangle(Point, Point, Point) 构造函数
 * double area() const 返回三角形函数
 * Circle outerCircle() 获取三角形的外接圆
 * 
 *         关于多边形的函数
 * Polygon(vector<Point>&) 构造函数
 * double circum();  求凸包的周长
 * void graham(Polygon&) 求凸包 传入值为需要求出的凸包的点集
 * int PointAndPolgon(Point&) 判断点与多边形的关系，0外 1内 2边上 3点上 （未实现）
 * double minRectCover() 点集的最小矩形覆盖，自己必须是（逆时针）凸包才能调用 （未实现）
 * Circle minCircleCover() 点集的最小圆覆盖
*/

using namespace std;
const double eps = 1e-8, PI = acos(-1.0);
int dcmp(double x) {
    if (fabs(x) < eps) return 0;
    return x > 0 ? 1 : -1;
}
/******************************点*******************************/
/*
除了结构体内部函数还有
angle(Point& a, Point& b) // 两个向量的夹角
*/
struct Point {
    double x, y;
    Point(double x = 0, double y = 0) : x(x), y(y) {}
    Point operator + (const Point&) const;
    Point operator - (const Point&) const;
    double operator * (const Point&) const;    // 点乘
    double operator ^ (const Point&) const;    // 叉乘
    bool operator == (const Point&) const;
    bool operator < (const Point&) const;  // 排序需要
    Point operator * (double);    //向量伸长b倍
    double len() const; // 向量的长度
    Point rotate(double);    // 向量逆时针旋转a弧度后
    Point rotate(Point&, double);    // 点绕p点顺时针旋转a弧度后
    void print() { printf("%.2f %.2f", x, y); }
    int init() { return scanf("%lf%lf", &x, &y); }
};
Point Point::operator + (const Point& b) const {
    return Point(x + b.x, y + b.y);
}
Point Point::operator - (const Point& b) const {
    return Point(x - b.x, y - b.y);
}
// 点乘
double Point::operator * (const Point& b) const { 
    return x * b.x + y * b.y;
}
// 叉乘
double Point::operator ^ (const Point& b) const {
    return x * b.y - y * b.x;
}
bool Point::operator == (const Point& b) const {
    return !dcmp(x - b.x) && !dcmp(y - b.y);
}
bool Point::operator < (const Point& b) const {
    return (!dcmp(x - b.x)) ? dcmp(y - b.y) < 0 : x < b.x;
}
// 向量的长度
double Point::len() const { return sqrt(x * x + y * y); }
//向量伸长b倍
Point Point::operator * (double b) {
    return Point(x * b, y * b);
}
// 向量逆时针旋转a弧度后
// cosx -sinx
// sinx cosx
Point Point::rotate(double a) {
    return Point(x * cos(a) - y * sin(a), x * sin(a) + y * cos(a));
}
// 点绕p点顺时针旋转a弧度后
Point Point::rotate(Point &p, double a) {
    Point vec = (*this) - p;
    return vec.rotate(a) + p;
}
// 两个向量的夹角
double angle(Point& a, Point& b) {
    return acos(a * b / a.len() / b.len());
}
/******************************线*******************************/
/*
除了结构体内部函数还有
int LineAndLine(Line& l1, Line&)  直线和直线的关系 0平行 1重合 2相交
Point getLineInter(const Line& l1, const Line& l2) 求两直线的交点,必须相交才能调用
int SegAndSeg(const Line& l1, const Line& l2) 两个线段的关系 0不相交 1非规范相交(其中一个线段的端点和另一个线段相交) 2规范相交
int LineAndSeg(const Line& line, const Line& seg) 直线和线段的关系，0不相交 1非规范相交 2规范相交
*/
struct Line {
    Point p1, p2, v;
    Line() {}
    Line(Point p1, Point p2) : p1(p1), p2(p2), v(p2 - p1) {}
    Line(Point, double); // 根据一个点和一个倾斜角0<= angle < PI确定直线
    double len(); // 线段的长度
    Point point(double);  // P = p1 + vt
    double angle(); // 直线的倾斜角[0, PI)
    double disPointToLine(const Point&); // 点到这条直线的距离
    double disPointToSeg(const Point&); // 点到这条线段的距离
    Point getPro(const Point&); // 点到这条线的投影
    Point getSym(const Point&); // 点关于这条线的对称点
    bool isOnLine(const Point&); // 点是否在这条直线上
    bool isOnSeg(const Point&); // 点是否在这条线段上
    Point cross(Line&); // 直线和这条直线的交点，前提是相交才能调用
    void print(); // 输出线段
};
Line::Line(Point p, double angle) : p1(p) {
    if (!dcmp(angle - PI / 2)) p2 = p1 + Point(0, 1);
    else p2 = p1 + Point(1, tan(angle));
}
double Line::len() { return v.len(); }
Point Line::point(const double t){ return (p1 + (v * t)); }
double Line::angle() {
    double ret = atan2(p2.y - p1.y, p2.x - p1.x);
    if (dcmp(ret) < 0) ret += PI;
    if (!dcmp(ret - PI)) ret -= PI;
    return ret;
}
void Line::print() {
    printf("(%f,%f)->(%f,%f)", p1.x, p1.y, p2.x, p2.y);
}
double Line::disPointToLine(const Point& p) {
    Point vec = p - p1;
    return fabs(v ^ vec) / v.len();
}
double Line::disPointToSeg(const Point& p) {
    if (p1 == p2) return (p1 - p).len();
    Point v1 = p - p1, v2 = p - p2;
    if (dcmp(v1 * v) < 0) return v1.len();
    if (dcmp(v2 * v) > 0) return v2.len();
    return disPointToLine(p);
}
Point Line::getPro(const Point& p) {
    return p1 + v * (v * (p - p1) / (v.len() * v.len()));
}
Point Line::getSym(const Point& p) {
    Point q = getPro(p);
    return Point(2 * q.x - p.x, 2 * q.y - p.y);
}
bool Line::isOnLine(const Point& p) {
    return !dcmp((p - p1) ^ (p - p2));
}
bool Line::isOnSeg(const Point& p) {
    return isOnLine(p) && (dcmp((p - p1) * (p - p2)) <= 0);
}
Point Line::cross(Line& l) {
    double a1 = l.v ^ (p1 - l.p1);
    double a2 = l.v ^ (p2 - l.p1);
    return Point((p1.x * a2 - p2.x * a1) / (a2 - a1),
    (p1.y *a2 - p2.y * a1) / (a2 - a1));
}
// 直线和直线的关系 0平行 1重合 2相交 
int LineAndLine(Line& l1, Line& l2) {
    if (!dcmp(l1.v ^ l2.v)) return l2.isOnLine(l1.p1);
    return 2;
}
// 求两个直线的交点，必须相交才能调用
Point getLineInter(Line& l1, Line& l2) {
    Point vec = l1.p1 - l2.p1;
    double t = (l2.v ^ vec) / (l1.v ^ l2.v);
    return l1.point(t);
}
// 判断两个线段的关系
int SegAndSeg(const Line& l1, const Line& l2) {
    int d1 = dcmp(l1.v ^ (l2.p1 - l1.p1));
    int d2 = dcmp(l1.v ^ (l2.p2 - l1.p1));
    int d3 = dcmp(l2.v ^ (l1.p1 - l2.p1));
    int d4 = dcmp(l2.v ^ (l1.p2 - l2.p1));
    if((d1 ^ d2) == -2 && (d3 ^ d4) == -2) return 2;
    return (!d1 && dcmp((l2.p1 - l1.p1) * (l2.p1 - l1.p2)) <= 0) || 
        (!d2 && dcmp((l2.p2 - l1.p1) * (l2.p2 - l1.p2)) <= 0) || 
        (!d3 && dcmp((l1.p1 - l2.p1) * (l1.p1 - l2.p2)) <= 0) ||
        (!d4 && dcmp((l1.p2 - l2.p1) * (l1.p2 - l2.p2)) <= 0);
}
// 直线和线段的关系，0不相交 1非规范相交 2规范相交
int LineAndSeg(const Line& line, const Line& seg) {
    int d1 = dcmp(line.v ^ (seg.p1 - line.p1));
    int d2 = dcmp(line.v ^ (seg.p2 - line.p1));
    if ((d1 ^ d2) == -2) return 2;
    return d1 == 0 || d2 == 0;
}
/******************************圆*******************************/
struct Circle {
    Point p;
    double r;
    Circle() {}
    Circle(Point p, double r) : p(p), r(r) {}
    Circle(Point, Point , Point);  // 过三点一个圆
    double area() const; // 面积
    double circum() const; // 周长
    int PointAndCircle(Point&); // 点和圆的关系 0圆外 1圆上 2圆内
    int LineAndCircle(Line&);  // 直线和圆的关系 0不相交 1相切 2相交两点
    int CircleAndCircle(Circle&); // 圆和圆的关系 0内含 1内切 2相交两点 3外切 4外离
};
double Circle::area() const { return PI * r * r; }
double Circle::circum() const { return 2 * PI *r; }
Circle::Circle(Point a, Point b, Point c) {
    Point v1 = b - a, v2 = c - b;
    Line l1((a + b) * 0.5, ((a + b) * 0.5) + Point(-v1.y, v1.x));
    Line l2((b + c) * 0.5, ((b + c) * 0.5) + Point(-v2.y, v2.x));
    p = getLineInter(l1, l2);
    r = (p - a).len();
}
int Circle::PointAndCircle(Point& a) {
    int d = dcmp((a - p).len() - r);
    if (d > 0) return 0;
    if (d < 0) return 2;
    return 1;
}
int Circle::LineAndCircle(Line& l) {
    int d = dcmp(l.disPointToLine(p) - r);
    if (d > 0) return 0;
    if (d < 0) return 2;
    return 1;
}
int Circle::CircleAndCircle(Circle& c) {
    double dist = (c.p - p).len();
    if (dcmp(dist - r - c.r) > 0) return 4;
    if (!dcmp(dist - r - c.r)) return 3;
    double l = fabs(r - c.r);
    if (dcmp(dist - r - c.r) < 0 && dcmp(dist - l) > 0) return 2;
    return dcmp(dist - l) == 0;
}
/****************************三角形******************************/
struct Triangle{
    Point p[3];
    Triangle() {}
    Triangle(Point A, Point B, Point C);
    double area() const ;  // 三角形面积
    Circle outerCircle(); // 外接圆
};
Triangle::Triangle(Point A, Point B, Point C) {
    p[0] = A, p[1] = B, p[2] = C;
}
double Triangle::area() const {
    double ret = 0;
    for (int i = 0; i < 3; ++i) {
        ret += p[i] ^ p[(i + 1) % 3];
    }
    return fabs(ret) / 2;
}
// r = a * b * c / 4S
// 两条边的中垂线的交点，也可用kuangbin的方法，但此方法不需要再次调用Line的函数
Circle Triangle::outerCircle() {
    double A1 = 2.0 * (p[1].x - p[0].x), B1 = 2.0 * (p[1].y - p[0].y);
    double A2 = 2.0 * (p[2].x - p[1].x), B2 = 2.0 * (p[2].y - p[1].y);
    double C1 = 0, C2 = 0;
    for (int i = 0, j = 1, k = -1; i < 2; ++i, ++j, k = 1) {
        C1 += (p[i].x * p[i].x + p[i].y * p[i].y) * k;
        C2 += (p[j].x * p[j].x + p[j].y * p[j].y) * k;
    }
    double x = ((C1 * B2) - C2 * B1) / ((A1 * B2) - A2 * B1);
    double y = ((A1 * C2) - A2 * C1) / ((A1 * B2) - A2 * B1);
    return Circle(Point(x, y), (Point(x, y) - p[0]).len());
}

/****************************多边形/点集******************************/

struct Polygon {
    vector<Point> p;
    // vector<Line> l;
    Polygon(){}
    Polygon(vector<Point>& p) : p(p) {};
    double circum(); // 求凸包的周长
    void graham(Polygon&); // 凸包
    int PointAndPolygon(Point&); // 判断点与多边形的关系，0外 1内 2边上 3点上
    double minRectCover(); // 最小矩形覆盖，自己必须是（逆时针）凸包才能调用
    Circle minCircleCover(); // 点集的最小圆覆盖
};
double Polygon::circum() {
    int n = p.size();
    if (n < 2) return 0.0;
    if (n == 2) return (p[0] - p[1]).len();
    double ret = 0;
    for (int i = 0; i < n; ++i) {
        ret += (p[(i + 1) % n] - p[i]).len();
    }
    return ret;
}
void Polygon::graham(Polygon& res) {
    int indx = 0, n = p.size();
    for (int i = 1; i < n; ++i) if (p[i] < p[indx]) indx = i;
    swap(p[0], p[indx]);
    sort(p.begin() + 1, p.end(), [&](Point& i, Point& j) {
        int d = dcmp(atan2(i.y - p[0].y, i.x - p[0].x) - atan2(j.y - p[0].y, j.x - p[0].x));
        if (d) return d < 0;
        return i.x < j.x;
    });
    res.p.emplace_back(p[0]);
    if (n == 1) return;
    res.p.emplace_back(p[1]);
    if (n == 2) {
        if (p[0] == p[1]) res.p.pop_back();
        return;
    }
    int x = res.p.size();
    for (int i = 2; i < n; ++i, ++x) {
        while (x >= 2 && dcmp((res.p[x - 2] - res.p[x - 1]) ^ (res.p[x - 2] - p[i])) <= 0) {
            --x;
            res.p.pop_back();
        }
        res.p.emplace_back(p[i]);
    }
    if (res.p.size() == 2 && (res.p[0] == res.p[1])) res.p.pop_back();
}
Circle Polygon::minCircleCover() {
    random_shuffle(p.begin(), p.end());
    Circle res(p[0], 0);
    int n = p.size();
    for (int i = 1; i < n; ++i) {
        if (res.PointAndCircle(p[i])) continue; // 在圆内
        res = Circle(p[i], 0);
        for (int j = 0; j < i; ++j) {
            if (res.PointAndCircle(p[j])) continue; // 在圆内
            res = Circle((p[i] + p[j]) * 0.5, (p[i] - p[j]).len() * 0.5);
            for (int k = 0; k < j; ++k) {
                if (res.PointAndCircle(p[k])) continue; // 在圆内
                res = Circle(p[i], p[j], p[k]);
            }
        }
    }
    return res;
}
vector<Point> vt;
int main() {
    int t;
    while(scanf("%d", &t), t) {
        Point p;
        for (int i = 0; i < t; ++i) {
            p.init();
            vt.emplace_back(p);
        }
        Polygon pol(vt), ans;
        pol.graham(ans);
        printf("%.2f\n", ans.circum());
        vt.clear();
    }
    return 0;
}
```



## 
