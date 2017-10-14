这里就涉及了TableView的重用机制，为了做到显示和数据分离，IOS tableView的实现并且不是为每个数据项创建一个tableCell。而是只创建屏幕可显示最大个数的cell，然后重复使用这些cell，对cell做单独的显示配置，来达到既不影响显示效果，又能充分节约内容的目的。下面简要分析一下它的实现原理。

重用实现分析

 查看UITableView头文件，会找到NSMutableArray\* visiableCells，和NSMutableDictnery\* reusableTableCells两个结构。visiableCells内保存当前显示的cells，reusableTableCells保存可重用的cells。

 TableView显示之初，reusableTableCells为空，那么tableView dequeueReusableCellWithIdentifier:CellIdentifier返回nil。开始的cell都是通过[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier]来创建，而且cellForRowAtIndexPath只是调用最大显示cell数的次数。

 比如：有100条数据，iPhone一屏最多显示10个cell。程序最开始显示TableView的情况是：

 1\. 用[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier]创建10次cell，并给cell指定同样的重用标识(当然，可以为不同显示类型的cell指定不同的标识)。并且10个cell全部都加入到visiableCells数组，reusableTableCells为空。

 2\. 向下拖动tableView，当cell1完全移出屏幕，并且cell11(它也是alloc出来的，原因同上)完全显示出来的时候。cell11加入到visiableCells，cell1移出visiableCells，cell1加入到reusableTableCells。

 3\. 接着向下拖动tableView，因为reusableTableCells中已经有值，所以，当需要显示新的cell，cellForRowAtIndexPath再次被调用的时候，tableView dequeueReusableCellWithIdentifier:CellIdentifier，返回cell1。cell1加入到visiableCells，cell1移出reusableTableCells；cell2移出visiableCells，cell2加入到reusableTableCells。之后再需要显示的Cell就可以正常重用了。

 所以整个过程并不难理解，但需要注意正是因为这样的原因：配置Cell的时候一定要注意，对取出的重用的cell做重新赋值，不要遗留老数据。

一些情况

 使用过程中，我注意到，并不是只有拖动超出屏幕的时候才会更新reusableTableCells表，还有：

 1\. reloadData，这种情况比较特殊。一般是部分数据发生变化，需要重新刷新cell显示的内容时调用。在cellForRowAtIndexPath调用中，所有cell都是重用的。我估计reloadData调用后，把visiableCells中所有cell移入reusableTableCells，visiableCells清空。cellForRowAtIndexPath调用后，再把reuse的cell从reusableTableCells取出来，放入到visiableCells。

 2\. reloadRowsAtIndex，刷新指定的IndexPath。如果调用时reusableTableCells为空，那么cellForRowAtIndexPath调用后，是新创建cell，新的cell加入到visiableCells。老的cell移出visiableCells，加入到reusableTableCells。于是，之后的刷新就有cell做reuse了。

TableView常用 code

－、建立 UITableView

 DataTable = [[UITableView alloc] initWithFrame:CGRectMake(0, 0, 320, 420)];

 [DataTable setDelegate:self];

 [DataTable setDataSource:self];

 [self.view addSubview:DataTable];

 [DataTable release];

二、UITableView各Method说明

//Section总数

- (NSArray \*)sectionIndexTitlesForTableView:(UITableView \*)tableView{

 return TitleData;

}

// Section Titles

//每个section显示的标题

- (NSString \*)tableView:(UITableView \*)tableView titleForHeaderInSection:(NSInteger)section{

 return @"";

}

//指定有多少个分区(Section)，默认为1

- (NSInteger)numberOfSectionsInTableView:(UITableView \*)tableView {

 return 4;

}

//指定每个分区中有多少行，默认为1

- (NSInteger)tableView:(UITableView \*)tableView numberOfRowsInSection:(NSInteger)section{

}

//绘制Cell

-(UITableViewCell \*)tableView:(UITableView \*)tableView cellForRowAtIndexPath:(NSIndexPath \*)indexPath {

static NSString \*SimpleTableIdentifier = @"SimpleTableIdentifier";

 UITableViewCell \*cell = [tableView dequeueReusableCellWithIdentifier:

 SimpleTableIdentifier];

 if (cell == nil) { 

 cell = [[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault

 reuseIdentifier: SimpleTableIdentifier] autorelease];

 }

 cell.imageView.image=image;//未选cell时的图片

 cell.imageView.highlightedImage=highlightImage;//选中cell后的图片

 cell.text=//.....

 return cell;

}

//行缩进

-(NSInteger)tableView:(UITableView \*)tableView indentationLevelForRowAtIndexPath:(NSIndexPath \*)indexPath{

 NSUInteger row = [indexPath row];

 return row;

}

//改变行的高度

- (CGFloat)tableView:(UITableView \*)tableView heightForRowAtIndexPath:(NSIndexPath \*)indexPath{

 return 40;

}

//定位

[TopicsTable setContentOffset:CGPointMake(0, promiseNum \* 44 + Chapter \* 20)];

//返回当前所选cell

NSIndexPath \*ip = [NSIndexPath indexPathForRow:row inSection:section];

[TopicsTable selectRowAtIndexPath:ip animated:YES scrollPosition:UITableViewScrollPositionNone];

[tableView setSeparatorStyle:UITableViewCellSelectionStyleNone];

//选中Cell响应事件

- (void)tableView:(UITableView \*)tableView didSelectRowAtIndexPath:(NSIndexPath \*)indexPath{

 [tableView deselectRowAtIndexPath:indexPath animated:YES];//选中后的反显颜色即刻消失

}

//判断选中的行（阻止选中第一行）

-(NSIndexPath \*)tableView:(UITableView \*)tableView willSelectRowAtIndexPath:(NSIndexPath \*)indexPath

{

 NSUInteger row = [indexPath row];

 if (row == 0)

 return nil;

 return indexPath;

}

//划动cell是否出现del按钮

- (BOOL)tableView:(UITableView \*)tableView canEditRowAtIndexPath:(NSIndexPath \*)indexPath {

}

//编辑状态

- (void)tableView:(UITableView \*)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle

forRowAtIndexPath:(NSIndexPath \*)indexPath

{

｝

[topicsTable setContentSize:CGSizeMake(0,controller.promiseNum \* 44)];

//右侧添加一个索引表

- (NSArray \*)sectionIndexTitlesForTableView:(UITableView \*)tableView{

}

//返回Section标题内容

- (NSString \*)tableView:(UITableView \*)tableView titleForHeaderInSection:(NSInteger)section{

}

//自定义划动时del按钮内容

- (NSString \*)tableView:(UITableView \*)tableView

titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath \*)indexPath

//跳到指的row or section

[tableView scrollToRowAtIndexPath:[NSIndexPath indexPathForRow:0 inSection:0] atScrollPosition:UITableViewScrollPositionBottom animated:NO];

三、在UITableViewCell上建立UILable多行显示

- (UITableViewCell \*)tableView:(UITableView \*)tableView cellForRowAtIndexPath:(NSIndexPath \*)indexPath {

 static NSString \*CellIdentifier = @"Cell"; 

 UITableViewCell \*cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];

 if (cell == nil) {

 cell = [[[UITableViewCell alloc] initWithFrame:CGRectZero reuseIdentifier:CellIdentifier] autorelease];

 UILabel \*Datalabel = [[UILabel alloc] initWithFrame:CGRectMake(10, 0, 320, 44)];

 [Datalabel setTag:100];

 Datalabel.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;

 [cell.contentView addSubview:Datalabel];

 [Datalabel release];

 } 

 UILabel \*Datalabel = (UILabel \*)[cell.contentView viewWithTag:100];

 [Datalabel setFont:[UIFont boldSystemFontOfSize:18]];

 Datalabel.text = [data.DataArray objectAtIndex:indexPath.row];

 cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;

 return cell;

}

//选中cell时的颜色

typedef enum {

 UITableViewCellSelectionStyleNone,

 UITableViewCellSelectionStyleBlue,

 UITableViewCellSelectionStyleGray

} UITableViewCellSelectionStyle 

//cell右边按钮格式

typedef enum {

 UITableViewCellAccessoryNone, // don't show any accessory view

 UITableViewCellAccessoryDisclosureIndicator, // regular chevron. doesn't track

 UITableViewCellAccessoryDetailDisclosureButton, // blue button w/ chevron. tracks

 UITableViewCellAccessoryCheckmark // checkmark. doesn't track

} UITableViewCellAccessoryType

//是否加换行线

typedef enum {

 UITableViewCellSeparatorStyleNone,

 UITableViewCellSeparatorStyleSingleLine

} UITableViewCellSeparatorStyle//改变换行线颜色

tableView.separatorColor = [UIColor blueColor]；