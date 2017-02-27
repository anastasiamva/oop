using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Text.RegularExpressions;
using System.IO;

namespace triangle
{
    class Point
    {
        readonly double X;
        readonly double Y;
        public Point(double x, double y)
        {
            this.X = x;
            this.Y = y;
        }

        public double GetX()
        {
            return X;
        }

        public double GetY()
        {
            return Y;
        }
    }
    class Edge
    {
        readonly Point A;
        readonly Point B;
        public Edge(Point a, Point b)
        {
            if (a.GetX() != b.GetX() || a.GetY() != b.GetY())
            {
                this.A = a;
                this.B = b;
            }
            else
            {
                Console.WriteLine("Попробуйте снова");
            }

        }

        public double Length
        {
            get
            {
                return Math.Sqrt(Math.Pow(A.GetX() - B.GetX(), 2) + Math.Pow(A.GetY() - B.GetY(), 2));
            }
        }
    }
    class Triangle
    {
        readonly Point A;
        readonly Point B;
        readonly Point C;
        readonly double abLength;
        readonly double bcLength;
        readonly double caLength;
        public Triangle(Point a, Point b, Point c)
        {
            Edge ab = new Edge(a, b);
            Edge bc = new Edge(b, c);
            Edge ca = new Edge(a, c);
            if (ab.Length + bc.Length <= ca.Length ||
                ab.Length + ca.Length <= bc.Length ||
                ca.Length + bc.Length <= ab.Length)
            {
                Console.WriteLine("Невозможно создать треугольник");
            }
            else
            {
                this.A = a;
                this.B = b;
                this.C = c;
                abLength = ab.Length;
                bcLength = bc.Length;
                caLength = ca.Length;
            }
        }

        public double Perimeter
        {
            get
            {
                return this.abLength + this.bcLength + this.caLength;
            }
        }

        public double Area
        {
            get
            {
                double halfPerimeter = this.Perimeter / 2;
                return Math.Sqrt(halfPerimeter * (halfPerimeter - this.abLength) *
                    (halfPerimeter - this.bcLength) * (halfPerimeter - this.caLength));
            }
        }

        double GetCorner(double caLength, double bcLength, double abLength)
        {
            return Math.Acos((Math.Pow(bcLength, 2) + Math.Pow(caLength, 2) - Math.Pow(abLength, 2)) /
                    (2 * caLength * bcLength)) * 57.2958;
        }

        public bool IsRight
        {
            get
            {
                double Error = 0.001;
                return Math.Abs(GetCorner(this.abLength, this.bcLength, this.caLength) - 90.0) <= Error ||
                   Math.Abs(GetCorner(this.caLength, this.bcLength, this.abLength) - 90.0) <= Error ||
                   Math.Abs(GetCorner(this.caLength, this.abLength, this.bcLength) - 90.0) <= Error;
            }

        }

        public bool IsIsosceles
        {
            get
            {
                return this.abLength == this.bcLength || this.abLength == this.caLength || this.caLength == this.bcLength;
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            Triangle[] triangle = new Triangle[3];
            triangle[0] = new Triangle(new Point(0, 0), new Point(4, 0), new Point(0, 5));
            triangle[1] = new Triangle(new Point(0, 0), new Point(3, 3), new Point(0, 5));
            triangle[2] = new Triangle(new Point(0, 0), new Point(4, 3), new Point(0, 6));

            Console.WriteLine(Avg.AvgArea(triangle));
            Console.WriteLine(Avg.AvgPerimeter(triangle));

        }
    }

    static class Avg
    {
        static public double AvgPerimeter(Triangle[] array)
        {
            int Counter = 0;
            double Sum = 0.0;
            for (int index = 0; index < array.Length; index++)
            {
                if (array[index].IsRight)
                {
                    Sum += array[index].Perimeter;
                    Counter++;
                }
            }
            return Sum / Counter;
        }

        static public double AvgArea(Triangle[] array)
        {
            int Counter = 0;
            double Sum = 0.0;
            for (int index = 0; index < array.Length; index++)
            {
                if (array[index].IsIsosceles)
                {
                    Sum += array[index].Area;
                    Counter++;
                }
            }
            return Sum / Counter;
        }
    }
}
