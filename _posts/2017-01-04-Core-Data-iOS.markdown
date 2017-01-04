---
layout: single
title:  "Core Data in iOS"
date:   2017-01-04 09:08:55 +0800
#categories: iOS

excerpt: "A Simplified Introduction of iOS Core Data: Initializing Core Data stack, Creating and Saving Managed Objects, Fetching Objects, Connecting the Model to Views and so forth."
header:
  teaser: /assets/images/codeback.jpg
  overlay_image: /assets/images/codeback.jpg
  overlay_filter: 0.6 # same as adding an opacity of 0.5 to a black background
  
---

## Initializing The Core Stack

### What is Core Stack?
**A collection of framework objects** that are accessed as part of the initialization of Core Data and that mediate between the objects in your application and external data stores.  
It handles all of the interactions with the external data stores so that your application can focus on its business logic.  
The stack consists of three primary objects:

+ <span style="color: white; background-color: #88ccee; padding:0.15em 0.5em 0.15em; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5)">
	``NSManagedObjectContext``
  </span>  
+ <span style="color: white; background-color: #4499ee; padding:0.15em 0.5em 0.15em; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5)">
	``NSPersistentStoreCoordinator``
  </span>  
+ <span style="color: white; background-color: #8866ff; padding:0.15em 0.5em 0.15em; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5)">
	``NSManagedObjectModel``
  </span>  




You need to do the followings to access your application data:

1. Create Data
2. Initialize the Core Data stack
3. Access Application Data

Example Core Data stack creation:
{% highlight objc %}
@interface MyDataController : NSObject
 
@property (strong) NSManagedObjectContext *managedObjectContext;
 
- (void)initializeCoreData;
 
@end
 
@implementation MyDataController
 
- (id)init
{
    self = [super init];
    if (!self) return nil;
 
    [self initializeCoreData];
 
    return self;
}
 
- (void)initializeCoreData
{
	//concatenate the data file name, 'DataModel.momd'
	NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"DataModel" withExtension:@"momd"];
	//use the URL to create a managed object model
	NSManagedObjectModel *mom = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
	NSAssert(mom != nil, @"Error initializing Managed Object Model");
 
	//create NSPersistentStoreCoordinator
	NSPersistentStoreCoordinator *psc = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:mom];
	//initiate the Managed Object Context
	NSManagedObjectContext *moc = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSMainQueueConcurrencyType];
	
	//set Coordinator for the context
	[moc setPersistentStoreCoordinator:psc];
	
	//set the instance's context with moc
	[self setManagedObjectContext:moc];
	
	NSFileManager *fileManager = [NSFileManager defaultManager];
	NSURL *documentsURL = [[fileManager URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
	NSURL *storeURL = [documentsURL URLByAppendingPathComponent:@"DataModel.sqlite"];
 
 	dispatch_async(dispatch_get_global_queue( DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^(void) {
		NSError *error = nil;
		NSPersistentStoreCoordinator *psc = [[self managedObjectContext] persistentStoreCoordinator];
		NSPersistentStore *store = [psc addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error];
		NSAssert(store != nil, @"Error initializing PSC: %@\n%@", [error localizedDescription], [error userInfo]);
	});
}
{% endhighlight %}

### <span style="color: white; background-color: #8866ff; padding:0.3em 0.5em 0.3em; border-radius: 10px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5);">NSManagedObjectModel</span>
NSManagedObjectModel defines the structure of the data, which is going to be accessed by the Core Data stack. In the sample code, ``mom`` is one instance of this kind, which is loaded into memory as the first step in the creation of the stack. The ``NSURL`` from the main application bundle using a known filename (in this example ``DataModel.momd``) for the NSManagedObjectModel.

Once the <span style="color: #f0f0f0; background-color: #8866ff; padding:0.06em 0.5em 0.06em; border-radius: 6px;">NSManagedObjectModel</span> object is intialized, the <span style="color: #f0f0f0; background-color: #4499ee; padding:0.06em 0.5em 0.06em; border-radius: 6px;">NSPersistentStoreCoordinator</span> object is constructed.

### <span style="color: white; background-color: #4499ee; padding:0.3em 0.5em 0.3em; border-radius: 10px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5)">NSPersistentStoreCoordinator</span>
The <span style="color: #f0f0f0; background-color: #4499ee; padding:0.06em 0.5em 0.06em; border-radius: 6px;">NSPersistentStoreCoordinator</span> sits in the middle of the Core Data stack, which is responsible for realizing instances of entities that are defined inside of the model. It creates new instances of the entities in the model, and it retrieves existing instances from a persistent store (*NSPersistentStore*{: style="color: #ff6666"}).

The persistent store can be on disk or in memory. Depending on the structure of the application, it is possible, although uncommon, to have more than one persistent store being coordinated by the <span style="color: #f0f0f0; background-color: #4499ee; padding:0.06em 0.5em 0.06em;border-radius: 6px;">NSPersistentStoreCoordinator</span>.

Whereas the <span style="color: #f0f0f0; background-color: #8866ff; padding:0.06em 0.5em 0.06em;border-radius: 6px;">NSManagedObjectModel</span> defines the structure of the data, the <span style="color: #f0f0f0; background-color: #4499ee; padding:0.06em 0.5em 0.06em;border-radius: 6px;">NSPersistentStoreCoordinator</span> realizes objects from the data in the persistent store and passes those objects off to the requesting <span style="color: #f0f0f0; background-color: #88ccee; padding:0.06em 0.5em 0.06em;border-radius: 6px;">NSManagedObjectContext</span>. 


### <span style="color: white; background-color: #88ccee; padding:0.3em 0.5em 0.3em; border-radius: 10px; box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.5)">NSManagedObjectContext</span>
The managed object context (
<span style="color: #f0f0f0; background-color: #88ccee; padding:0.06em 0.5em 0.06em;border-radius: 6px;">
	NSManagedObjectContext
</span>
) is the object that your application will be interacting with the most, and therefore it is the one that is exposed to the rest of your application. 

It is just like an intelligent scratch pad. When you fetch objects from a persistent store, you bring temporary copies onto the scratch pad where they form an object graph (or a collection of object graphs). You can then modify those objects however you like. Unless you actually save those changes, however, the persistent store remains unaltered.

All managed objects must be registered with a managed object context. You use the context to add objects to the object graph and remove objects from the object graph. The context tracks the changes you make, both to individual objects’ attributes and to the relationships between objects. By tracking changes, the context is able to provide undo and redo support for you. It also ensures that if you change relationships between objects, the integrity of the object graph is maintained.

If you choose to save the changes you have made, the context ensures that your objects are in a valid state. If they are, the changes are written to the persistent store (or stores), new records are added for objects you created, and records are removed for objects you deleted.


## Fetching Objects
{% highlight objc %}
NSManagedObjectContext *moc = …;
NSFetchRequest *request = [NSFetchRequest fetchRequestWithEntityName:@"Employee"];
 
NSError *error = nil;
NSArray *results = [moc executeFetchRequest:request error:&error];
if (!results) {
    NSLog(@"Error fetching Employee objects: %@\n%@", [error localizedDescription], [error userInfo]);
    abort();
}
{% endhighlight%}

