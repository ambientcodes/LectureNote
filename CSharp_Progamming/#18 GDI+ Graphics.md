# [Inflearn] C# 프로그래밍 - #18강 GDI+ Graphics
[TOC]
## ** #18강 GDI+ Graphics **
### 1. GDI+
- GDI & GDI+
	: GDI(Graphics Device Interface) → os의 영역
    : GDI+ → .NET은 Graphics가 담당


### 2. Graphics
- Graphics 사용 방법 2가지
	- paint() 이벤트
		: e.Graphics()
    - CreateGraphics() 사용
    	: Graphics 참조 변수 생성

- CreateGraphics() 언제 사용?
	: paint 이벤트 외의 메서드

- CreateGraphics() 사용
	- 원형
		: public Graphics CreateGraphics()
        (namespace → System.Windows.Forms)
		: 사용한 후에는 되도록 Graphics.Dispose() 호출
- 사용하는 방법
	- Graphics 객체를 참조
	- Graphics 메서드로 출력

- 마우스 클릭마다 원 출력
	- 방법1) paintevent에서 처리
	```
    public partial class Form1 : Form
    {
        Point[] ptCircle;
        int nCount;

        public Form1()
        {
            InitializeComponent();
            ptCircle = new Point[100];
        }
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            for (int i = 0; i < nCount; i++)
            {
                e.Graphics.DrawEllipse(Pens.Black, ptCircle[i].X - 10, ptCircle[i].Y - 10, 20, 20);
            }
        }

        private void Form1_MouseClick(object sender, MouseEventArgs e)
        {
            ptCircle[nCount].X = e.X;
            ptCircle[nCount].Y = e.Y;
            nCount++;
            // WM_Paint 실행
            Invalidate();
        }
    }
    ```
	- 방법2) CreateGraphics() 사용 처리
	```
    public partial class Form1 : Form
    {
    	Point[] ptCircle;
    	int nCount;

        public Form1()
        {
            InitializeComponent();
            ptCircle = new Point[100];
        }

        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            for (int i = 0; i < nCount; i++)
            {
                e.Graphics.DrawEllipse(Pens.Black, ptCircle[i].X - 10, ptCircle[i].Y - 10, 20, 20);
            }
        }

        private void Form1_MouseClick(object sender, MouseEventArgs e)
        {
            ptCircle[nCount].X = e.X;
            ptCircle[nCount].Y = e.Y;

            Graphics g = CreateGraphics();
            g.DrawEllipse(Pens.Black, ptCircle[nCount].X - 10, ptCircle[nCount].Y - 10, 20, 20);
            nCount++;
            g.Dispose();
        }
    }
    ```

### 3. Color
- 구조체 정의
	- Represents an ARGB(alpha, red, green, blue) color, alpha는 투명/불투명의 정도
		- ```public struct color```
	- inheritance : Object → ValueType → Color

- Color 구조체
	- 사용 용도
		- 펜, 브러시의 색상 설정에 사용
    - Color 속성 사용하여 색상 설정
    	- AliceBlue, AntiqueWhite, Aqua 등 속성을 통한 색상 설정, Color 리턴
    	- ```public static System.Drawing.Color Aqua { get; }```
	- 사용자 정의할 수 있는 색상
		- FromArgb()
		- FromArgb(int alpha, int red, int green, int blue); → Overload

	- 시스템 색상에서 A, R, G, B 가져오기
		→ Color.색상.A. Color.색상.R, Color.색상.G, Color.색상.B
        - public byte A { get; }
        - public byte R { get; }
        - public byte G { get; }
        - public byte B { get; }

	- Blue Color의 A, R, G, B 출력
	```
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            string str = "Alpha:" + Color.Blue.A + " Red:" + Color.Blue.R + " Green:" + Color.Blue.G + " Blue:" + Color.Blue.B;
            e.Graphics.DrawString(str, Font, Brushes.Black, 10, 10);
        }
    }
	```

### 4. Pen
- Pen vs Pens
	- Pen → 생성
	- Pens → 기본 제공 펜
- Pen 생성자
	- Pen(Brush), Pen(Color)
	- Pen(Brush, Sigle), Pen(Color, Single)
- Pen 해제
	- Dipose();
- Pen과 Pens 사용 예
	```
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            Color myColor = Color.FromArgb(255, 0, 0);
            Pen newPen = new Pen(myColor);
            newPen.Width = 5.0f;    // new Pen(myColor, 5.0f);
            e.Graphics.DrawLine(newPen, 10, 10, 100, 10);
            e.Graphics.DrawLine(Pens.Blue, 10, 20, 100, 20);
            newPen.Dispose();
        }
    }
    ```

- 펜 스타일
	- DashStyle 속성 사용
		- ```public System.Drawing.Drawing2D.DashStyle DashStyle { get; set; }```
	- using System.Drawing.Drawing2D 선언
	- DashStyle 열거형 리턴
		- Custom
		- Dash
		- DashDot
		- DashDotDot
		- Dot
		- Solid
    - DashDot와 Dot 선 스타일 지정, 너비 3으로 설정
	```
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            Pen myPen = new Pen(Color.Black, 3.0f);
            myPen.DashStyle = DashStyle.DashDot;
            e.Graphics.DrawLine(myPen, 10, 10, 200, 10);
            myPen.DashStyle = DashStyle.Dot;
            e.Graphics.DrawLine(myPen, 10, 20, 200, 20);
        }
    }
    ```


### 5. Brush
- 역할
	- 도형 내부를 색 또는 패턴으로 채우는 역할
- 브러시 종류
	- SolidBrush, HatchBrush, TextureBrush... 등
	- 브러시를 요구하는 메서드의 공통점 (Fill~~~ 로 시작)

- SolidBrush 단색 브러시
	- 도형 전체를 한가지 색으로 채우는 브러시
	- 파란색으로 채우는 원 출력
	```
    public partial class Form1 : Form
    {
        public Form1()
        {
            InitializeComponent();
        }
        private void Form1_Paint(object sender, PaintEventArgs e)
        {
            SolidBrush myBrush = new SolidBrush(Color.Blue);
            e.Graphics.FillEllipse(myBrush, 10, 10, 100, 100);
            myBrush.Dispose();
        }
    }
    ```
- HatchBrush 패턴 브러시
	- 격자 패턴으로 도형을 채우는 브러시
	- ```using System.Drawing.Drawing2D;```
	- 전경색은 빨간색, 배경은 파란색으로 출력
	```
    public partial class Form1 : Form
    {
    	public Form1()
    	{
    	    InitializeComponent();
    	}
		private void Form1_Paint(object sender, PaintEventArgs e)
		{
			HatchBrush Hash = new HatchBrush(HatchStyle.Plaid, Color.Red, Color.Blue);
        	e.Graphics.FillRectangle(Hash, 10, 10, 200, 200);
        	Hash.Dispose();
		}
    }
    ```




