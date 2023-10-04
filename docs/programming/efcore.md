# Entity Framework Core

Entity Framework Core is an ORM (Object Relational Mapper), which is a library helping developer to handle links to database.
It handles automatic mapping between database tables (relational) and "Plain Old CLR Objects" (POCO, POJO in Java, etc.).

## Entity Framework Core in Action

Book written by Jon Smith

### Chapter 2: Querying database

Objects to create:

- *Entity Models*: C# objects mapped to the database scheme, which will represent tables
- `DBContext`: configuring database (connection string, perf, etc.) + declaring the Entity Models
- *DTO objects* (for "Data Transfering Object"): objects aiming at containing data for transferring it between 2 parts of the library
- a function `IQueryable<BookDto> MapBookToDto(IQueryable<Book>)` which will transform the SQL Query into a DTO object for presentation
- sorting, filtering, paging functions: `IQueryable<BookDto> Filtering(IQueryable<BookDto>, FilteringOptions)`

Layered Architecture: for small-to-medium sized projects:

- a DataLayer: `DBContext` + Entity Models. Has no knowledge of any other layer
- a ServiceLayer: implementing the `MapBookToDto` function + filtering, sorting, paging functions
- a PresentationLayer (e.g. ASP.Net Core): calling the functions defined in the ServiceLayer and send the DTO objects to the views (e.g. HTML pages)