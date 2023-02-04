## Spring AOP(面向切面編程)
>參考網址：https://zhuanlan.zhihu.com/p/104520344
>
>AOP 即Aspect Oriented Programming，意為：面向切面編程，通過預編譯方式和運行期動態代理實現程序功能的統一維護的一種技術。AOP 是OOP 的延續，是軟件開發中的一個熱點，也是Spring 框架中的一個重要內容，是函數式編程的一種衍生範型。利用AOP 可以對業務邏輯的各個部分進行隔離，從而使得業務邏輯各部分之間的耦合度降低，提高程序的可重用性，同時提高了開發的效率。
>它是面向對象編程的一種補充，主要應用於處理一些具有橫切性質的系統級服務，如日誌收集、事務管理、安全檢查、緩存、對像池管理等。
>
>* Joinpoint（連接點）：在系統運行之前，AOP 的功能模塊都需要織入到具體的功能模塊中。要進行這種織入過程，我們需要知道在系統的哪些執行點上進行織入過程，這些將要在其之上進行織入操作的系統執行點就稱之為Joinpoint，最常見的Joinpoint 就是方法調用。
>
>* Pointcut（切點）：用於指定一組Joinpoint，代表要在這一組Joinpoint 中織入我們的邏輯，它定義了相應Advice 將要發生的地方。通常使用正則表達式來表示。對於上面的例子，Pointcut 就是表示“所有要加入日誌記錄的接口” 的一個“表達式”。例如：“execution(* com.joonwhee.open.demo.service..*.*(..))”。
>
>* Advice（通知/增強）：Advice 定義了將會織入到Joinpoint 的具體邏輯，通過@Before、@After、@Around 來區別在JointPoint 之前、之後還是環繞執行的代碼。
>
>* Aspect（切面）：Aspect 是對系統中的橫切關注點邏輯進行模塊化封裝的AOP 概念實體。類似於Java 中的類聲明，在Aspect 中可以包含多個Pointcut 以及相關的Advice 定義。
>
>* Weaving（織入）：織入指的是將Advice 連接到Pointcut 指定的Joinpoint 處的過程，也稱為：將Advice 織入到Pointcut 指定的Joinpoint 處。
>
>* Target（目標對象）：符合Pointcut 所指定的條件，被織入Advice 的對象

**AccountDAO.java程式碼，簡單打印訊息，以便了解消息來自於何處**
```
@Component
public class AccountDAO {

	public void addAccount(Account theAccount, boolean vipFlag) {
		
		System.out.println(getClass() + ": DOING MY DB WORK: ADDING AN ACCOUNT");
	}
	
	public boolean doWork() {
		
		System.out.println(getClass() + ": doWork()");
		return false;
	}
}
```

**DemoConfig.java配置類別，啟用Aspect J自動代理以及給定類別掃描路徑**
```
@Configuration
@EnableAspectJAutoProxy
@ComponentScan("com.luv2code.aopdemo")
public class DemoConfig {

}
```
**使用@Before advice，在給定切入點方法執行前**
```
@Aspect
@Component
public class MyDemoLoggingAspect {

	// this is where we add all of our related advices for logging
	
	// let's start with an @Before advice
	
	@Before("execution(* com.luv2code.aopdemo.dao.*.*(..))")
	public void beforeAddAccountAdvice() {
		
		System.out.println("\n=====>>> Excuting @Before advice on method()");
	}
}
```
**MainDemoApp.java主應用程序，讀取配置類別，使用AccountDAO方法，透過AOP中@Before Advice，在方法執行前會先執行beforeAddAccountAdvice()**
```
public class MainDemoApp {

	public static void main(String[] args) {
		
		// read spring config java class
		AnnotationConfigApplicationContext context = 
				new AnnotationConfigApplicationContext(DemoConfig.class);
		
		// get the bean from spring container
		AccountDAO theAccountDAO = context.getBean("accountDAO", AccountDAO.class);
		
		// call the business method
		Account myAccount = new Account();
		theAccountDAO.addAccount(myAccount, true);
		theAccountDAO.doWork();
		
		// close the context
		context.close();
	}
}
```
![image](https://user-images.githubusercontent.com/101872264/216758904-45a45f14-6cb4-4066-9cf9-1e1309f06a34.png)
