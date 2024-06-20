**Issue: Start Code First Approach project (MVC)**

Add Model Customer make sure that model has [Key]

Add DataContext class inherit from DbContext 
 public class DataContext:DbContext
    {
        public DataContext() 
            : base("DefaultConnection") 
        { 
        }

        public DbSet<Customer> Customer { get; set; }

 private void FixEfProviderServicesProblem()
        {
                      var instance = System.Data.Entity.SqlServer.SqlProviderServices.Instance;
        }
    }

Add Connection String
<connectionStrings>
		<add name="DefaultConnection" connectionString="server=DESKTOP-EOG6839; Database=CodeFirstDb; User Id=sa; Password=sa@123;" providerName="System.Data.SqlClient" />
	</connectionStrings>


Run the command  in nuget console make sure you have selected DBcontext project
Enable-Migrations  
add-migration InitialCreate  
update-database 

Create Service Layer
Create ICustomerService (Interface)

public interface ICustomerServices
    {
         List<Customer> GetCustomers();
    }

Create Customer Service 

 public List<Customer> GetCustomers()
        {
            return _context.Customer.ToList();
        }

Install UNITY.MVC5 nuget package in main project that will auto add Unityconfig.cs file

Register your service and interface in Unityconfig.cs


 public static class UnityConfig 
    {
        public static void RegisterComponents()
        {
			var container = new UnityContainer();

            // register all your components with the container here
            // it is NOT necessary to register your controllers

            // e.g. container.RegisterType<ITestService, TestService>();

            container.RegisterType<ICustomerServices, CustomerServices>();

            DependencyResolver.SetResolver(new UnityDependencyResolver(container));
        }
    }

Add  UnityConfig method in Application Start method in Global.asax file

  protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
            UnityConfig.RegisterComponents();
        }




Add the following Code in dbcontext  if entityfram work error occurred
 private void FixEfProviderServicesProblem()
        {
                      var instance = System.Data.Entity.SqlServer.SqlProviderServices.Instance;
        }


Create HomeController 

 ICustomerServices _customerServices;

        public HomeController(ICustomerServices  customerServices) //constructor
        {
            _customerServices = new customerServices();
        }

[HttpGet]
        public JsonResult GetCustomer()
        {
            return Json(_customerServices.GetCustomers(), JsonRequestBehavior.AllowGet);
        }



**Issue: Start Code First Approach project (Latest .Net Core version )**
OLD Version Follow https://www.c-sharpcorner.com/article/microservice-using-asp-net-core/

Install-Package Microsoft.EntityFrameworkCore in Data Layer

Add AppDbContext Class in Data Layer
 public class AppDbContext : DbContext
 {
     public AppDbContext(DbContextOptions<AppDbContext> options):base(options) 
     {
             
     }

     public DbSet<School> Schools { get; set; }

     public DbSet<Student> Students { get; set; }

 }



Create Blank Database CodeFirstCore

Add Connection String

 "ConnectionStrings": {
   "DefaultConnection": "Server=DESKTOP-EOG6839;User ID=sa; Password=sa@123; Database=CodeFirstCore;TrustServerCertificate=True"
 }



Install both in the Main Layer
Install-Package Microsoft.EntityFrameworkCore.SqlServer 
Install-Package Microsoft.EntityFrameworkCore.Design


Add the following lines to the program.cs
builder.Services.AddDbContext<AppDbContext>(option =>
    option.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")));


exec
Add-Migration
Update-database


Add Dependency Injection in Program.cs
builder.Services.AddScoped<IStudentRepository,StudentRepository>();
