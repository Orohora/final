# final

# 모의 담금질 기법(Simulated Annealing)

 모의 담그질(Simulated Annealing) 기법은 높은 온도에서 액체 상태인 물질이 온도가 점차 낮아지면서 결정체로 변하는 과정을 모방한 해 탐색 알고리즘이다.



### Problem 1 : 함수의 전역 최적점을 찾을 수 있는 모의 담금질 기법 구현하기

 책에서는 2차 함수인 f(x)=-x^2+38x+80의 최댓값을 구하는 것으로 예시를 들었다. 하지만 함수의 차수가 커지게 되면 함수의 정의역이 실수 전체가 될 수 있으므로 최댓값과 최솟값을 구할 수 없을 수 있다.

![](C:\Users\cjh00\OneDrive\바탕 화면\함수1.PNG)

3차 함수인 y=x^3+3x^2+2x+4이다. 이 함수는  x가 실수 전체일때 최솟값과 최댓값을 가지지 않는다.



 이처럼 최대 차수가 홀수인 함수(1차, 3차 5차....)는 x의 구간을 한정시켜주지 않으면 최적값을 찾아낼 수 없다. 하지만 최대 차수가 짝수인 경우 함수의 종류에 따라 최대값을 가질 수 있다. 이번에는 4차 함수 중에서 최댓값을 가지는 함수인 f(x)=-3x^4+4x^3-1의 최댓값을 모의 담금질 기법을 이용해 구해보도록 한다.



### <코드>

#### Main.java

```
package com.company;

public class Main {
    public static void main(String[] args) {

        SimulatedAnnealing sa = new SimulatedAnnealing(10);
        Problem p = new Problem() {
            @Override
            public double fit(double x) {
                return -3*x*x*x*x+4*x*x*x-1 ;
            }

            @Override
            public boolean isNeighborBetter(double f0, double f1) {
                return f0 < f1;
            }
        };
        double x = sa.solve(p, 100, 0.99, 0, 0, 31);
        System.out.print("최솟값을 갖기 위한 x의 값은: ");
        System.out.println(x);
        System.out.print("최솟값: ");
        System.out.println(p.fit(x));
        System.out.println(sa.hist);


    }
}
```

#### Problem.java

```
package com.company;

public interface Problem {
    double fit(double x);
    boolean isNeighborBetter(double f0, double f1);
}
```



#### SimulatedAnnealing.java

```
package com.company;

import java.util.ArrayList;
import java.util.Random;

public class SimulatedAnnealing {
    private int niter;
    public ArrayList<Double> hist;

    public SimulatedAnnealing(int niter) {
        this.niter = niter;
        hist = new ArrayList<>();
    }

    public double solve(Problem p, double t, double a, double lower, double upper) {
        Random r = new Random();
        double x0 = r.nextDouble() * (upper - lower) + lower;
        return solve(p, t, a, x0, lower, upper);
    }

    public double solve(Problem p, double t, double a, double x0, double lower, double upper) {
        Random r = new Random();
        double f0 = p.fit(x0);
        hist.add(f0);

        for (int i=0; i<niter; i++) {
            int kt = (int) t;
            for(int j=0; j<kt; j++) {
                double x1 = r.nextDouble() * (upper - lower) + lower;
                double f1 = p.fit(x1);

                if(p.isNeighborBetter(f0, f1)) {
                    x0 = x1;
                    f0 = f1;
                    hist.add(f0);
                } else {
                    double d = Math.sqrt(Math.abs(f1 - f0));
                    double p0 = Math.exp(-d/t);
                    if(r.nextDouble() < 0.0001) {
                        x0 = x1;
                        f0 = f1;
                        hist.add(f0);
                    }
                }
            }
            t *= a;
        }
        return x0;
    }
}
```



### 실행 결과

![결과](C:\Users\cjh00\Desktop\결과.PNG)

실행 결과에 의하면 x가 약 0.9일 때 함수는 최솟값 0.1을 갖는다.

### 실제 결과 확인(수작업)

![함수](C:\Users\cjh00\Desktop\함수.jpg)



### Problem 2 : 종속 변수 데이터를 구한후 파라미터 측정하기

 모의 담금질을 이용한 종속 변수 데이터로 예전에 화학 실험을 했던 자료를 사용하기로 했습니다. 실험 내용은 실험실에 주어진 미지의 물체를 알아보기 위해 물체를 시험관에 물과 섞어 넣어 가열시켜 온도의 변화를 측정하고 결과값에 의해 녹는점을 측정하고 물질의 정체를 측정하는 것이었습니다. 참고로 사용된 물질은 팔미트산(plamitic acid)였고 아래의 사진은 당시의 결과값, 그래프입니다.

![실험2](C:\Users\cjh00\Desktop\실험2.jpg)

![실험1](C:\Users\cjh00\Desktop\실험1.jpg)

기존의 실험에서는 온도의 변화가 없는 시점을 녹는점으로 측정했는데 모의 담금질을 이용하기 위해 그래프를 온도에 따른 온도가 나타난 개수를 하여 온도의 개수가 가장 많다는 것이 변화가 없다는 것을 의미하기 때문에 그때의 온도값이 녹는점임을 알 수 있습니다. 아래는 온도에 따른 개수의 표에 대한 자료입니다.

| 온도(℃) | 개수 : 시간(분) |
| ------- | --------------- |
| 34℃     | 1               |
| 35℃     | 1               |
| 36℃     | 1               |
| 37℃     | 2               |
| 38℃     | 1               |
| 39℃     | 1               |
| 40℃     | 1               |
| 41℃     | 1               |
| 42℃     | 2               |
| 43℃     | 1               |
| 44℃     | 1               |
| 45℃     | 1               |
| 46℃     | 2               |
| 47℃     | 1               |
| 48℃     | 1               |
| 49℃     | 2               |
| 50℃     | 1               |
| 51℃     | 2               |
| 52℃     | 1               |
| 53℃     | 2               |
| 54℃     | 12              |
| 55℃     | 6               |
| 56℃     | 1               |
| 57℃     | 4               |
| 58℃     | 2               |
| 59℃     | 1               |
| 60℃     | 1               |
| 62℃     | 1               |
| 63℃     | 1               |
| 66℃     | 1               |
| 68℃     | 1               |
| 70℃     | 1               |
| 71℃     | 2               |

![image-20210611222943071](C:\Users\cjh00\AppData\Roaming\Typora\typora-user-images\image-20210611222943071.png)

이 실험을 통해 54도에서 가장 많이 관측됬음을 알 수 있고 따라서 54도가 녹는점임을 알 수 있습니다.



##### 전체 코드

코드를 새롭게 만들어서 시도해봤으나 코드를 완성시키지 못했습니다. 완성시키지 못한 전체 코드는 링크를 첨부하겠습니다.

링크 :  [Melt (github.com)](https://gist.github.com/Orohora/5ab5e85922fc76ab04fbe4d37f37e8ec)

```
package com.company;

public class Find{

    private ArrayList find = new ArrayList<Melt>();
    private int distance = 0;


    public Find(){
        for (int i = 0; i < FindMelt.numberOfCities(); i++) {
            find.add(null);
        }
    }

    public Find(ArrayList tour){
        this.tour = (ArrayList) tour.clone();
    }
public ArrayList getMelt(){
        return melt;
    }


    public void generateIndividual() {

        for (int meltIndex = 0; meltIndex < FindMelt.numberOfCities(); meltIndex++) {
            setCity(meltIndex, Findmelt.getCity(meltIndex));
        }

        Collections.shuffle(melt);
    }


    public Melt getMelt(int findPosition) {
        return (Melt)find.get(findPosition);
    }

    public void setCity(int tourPosition, Melt melt) {
        find.set(tourPosition, melt);
        distance = 0;
    }

    public int getDistance(){
        if (distance == 0) {
            int tourDistance = 0;
            for (int cityIndex=0; cityIndex < findSize(); cityIndex++) {
                Melt fromCity = getMelt(cityIndex);
                Melt destinationMelt;

                if(cityIndex+1 < findSize()){
                    destinationMelt = getMelt(cityIndex+1);
                }
                else{
                    destinationMelt = getMelt(0);
                }
                tourDistance += fromCity.distanceTo(destinationMelt);
            }
            distance = tourDistance;
        }
        return distance;
    }

    // Get number of cities on our tour
    public int findSize() {
        return find.size();
    }

    @Override
    public String toString() {
        String geneString = "|";
        for (int i = 0; i < tourSize(); i++) {
            geneString += getCity(i)+"|";
        }
        return geneString;
    }
}
```

