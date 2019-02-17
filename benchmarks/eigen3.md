Eigen3 (pure-header library)
===

```c++
#include <iostream>
#include <Eigen/Dense>
using namespace std;

#define N 4096

//__attribute__((target_clones("arch=haswell", "arch=sandybridge", "arch=nehalem", "default")))
int main(void)
{
	auto A = Eigen::MatrixXd::Random(N, N);
	auto B = Eigen::MatrixXd::Random(N, N);
	//A(0,0); B(0,0);
	auto C = A * B;
	//cout << A << endl << B << endl << C << endl;
	(void) C(0,0);
	return 0;
}
```
