## table基本用法


````objc

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {

    // Return the number of sections.
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

    // Return the number of rows in the section.
    return data.count;

}
//行高度
-(CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath
{
    //动态控制cell的高度
    //UITableViewCell *cell = [self tableView:tableView cellForRowAtIndexPath:indexPath];
    //return cell.frame.size.height;
     
    return 120;
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
  
    NSString *cellIdentifier = @"cell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellIdentifier];
    if (cell == nil)
    {
       cell = [[UITableViewCell alloc]initWithStyle:UITableViewCellStyleValue1 reuseIdentifier:cellIdentifier];
    }
    //#import <SDWebImage/UIImageView+WebCache.h>
    [cell.imageView setImageWithURL:[NSURL URLWithString:@"http://www.jjmc.com.cn/images/navi_figure_03.png"]
                   placeholderImage:[UIImage imageNamed:@"goodsHot"]];//左侧图片
    //配置cell样式
    cell.textLabel.text = [self.data objectAtIndex:[indexPath row]];//主标题
    cell.detailTextLabel.text = @"detailslable";//副标题
    //cell.imageView.image = [UIImage imageNamed:@"goodsHot"];//左侧图片
    cell.accessoryView =  [[UIImageView alloc]initWithImage:[UIImage imageNamed:@"goodsHot"]];//右侧图片
    cell.accessoryType = UITableViewCellAccessoryDisclosureIndicator;
    return cell;

}

````

## 自定义UITableViewCell有两种方法：

1:利用xib

````objc

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
  
    static NSString *cellIdentifier=@"name";
    BOOL nibsRegistered=NO;
    if (!nibsRegistered) {
        UINib *nib=[UINib nibWithNibName:@"TableViewCell1" bundle:nil];
        [tableView registerNib:nib forCellReuseIdentifier:cellIdentifier];
        nibsRegistered=YES;
    }
    TableViewCell1 *cell=[tableView dequeueReusableCellWithIdentifier:cellIdentifier];
    //cell 上的元素初始化代码
    return cell;
}

````

2:使用NSBundle


````objc
 
    static NSString *tableCellIdentifier = @"name";
    TableViewCell1 *cell = (TableViewCell1 *)[tableView dequeueReusableCellWithIdentifier:tableCellIdentifier];
    if(cell == nil){
        NSArray *nib = [[NSBundle mainBundle]loadNibNamed:@"TableViewCell1" owner:self options:nil];
        for(id oneObject in nib){
            if([oneObject isKindOfClass:[TableViewCell1 class]]){
                cell = (TableViewCell1 *)oneObject;
            }
        }
    }
    cell.lable1.text = [self.data objectAtIndex:indexPath.row];
    return cell;

2.1:无nib文件
 static NSString *CellIdentifier = @"Cell";
    
    PostTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
    if (!cell) {
        cell = [[PostTableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:CellIdentifier];
    }
    
    cell.post = [self.posts objectAtIndex:(NSUInteger)indexPath.row];
    
    return cell;

    //initWithStyle 方法
    self = [super initWithStyle:style reuseIdentifier:reuseIdentifier];
    if (!self) {
        return nil;
    }
````



3:使用故事版 （注意1:设置Identifier2:设置prototype cell 1 3：配置cell的class）


````objc

1 -(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
2 {
3     //这个是在storyboard中设置的identifier
4     static NSString *tableCellIdentifier = @"name";
5     MyCell *cell = (MyCell *)[tableView dequeueReusableCellWithIdentifier:tableCellIdentifier];
6     //cell初始化
7     return cell;
8 }




#pragma mark - Table view data source

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
#warning Potentially incomplete method implementation.
    // Return the number of sections.
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
#warning Incomplete method implementation.
    // Return the number of rows in the section.
    return self.flilterData.count;
}


- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    
    static NSString *tableCellIdentifier = @"name";
    TableViewCell1 *cell = (TableViewCell1 *)[tableView dequeueReusableCellWithIdentifier:tableCellIdentifier];
    if(cell == nil){
        NSArray *nib = [[NSBundle mainBundle]loadNibNamed:@"TableViewCell1" owner:self options:nil];
        for(id oneObject in nib){
            if([oneObject isKindOfClass:[TableViewCell1 class]]){
                cell = (TableViewCell1 *)oneObject;
            }
        }
    }
    cell.lable1.text = [self.flilterData objectAtIndex:indexPath.row];
    return cell;
}

````


## 搜索查询

````objc

-(void)searchBarSearchButtonClicked:(UISearchBar *)searchBar{

   // NSLog(@"%@",searchBar.text);
}
-(void)searchBar:(UISearchBar *)searchBar textDidChange:(NSString *)searchText{
      NSLog(@"%@",searchBar.text);
    [self filterContentForSearchbar:searchText];
}

-(void)searchBarCancelButtonClicked:(UISearchBar *)searchBar{

      NSLog(@"cancel");
    
}

-(void)filterContentForSearchbar:(NSString *)searchText{
    if([searchText length]==0){
        //查询所有
        self.flilterData = [NSArray arrayWithArray:self.data];
    }
    else{
        
        NSPredicate *predicate = [NSPredicate predicateWithFormat:@"SELF CONTAINS %@",searchText];
        self.flilterData = [self.flilterData filteredArrayUsingPredicate:predicate];
                                  
        
    }
}

#pragma mark 索引列表
- (NSArray *)sectionIndexTitlesForTableView:(UITableView *)tableView
{
    //set color
    tableView.sectionIndexColor = [UIColor redColor];
    tableView.sectionIndexBackgroundColor = [UIColor clearColor];
    
    NSMutableArray *toBeReturned = [[NSMutableArray alloc]init];
    for(char c = 'A';c<='Z';c++)
        
        [toBeReturned addObject:[NSString stringWithFormat:@"%c",c]];
    
    return toBeReturned;
    
}
- (NSInteger)tableView:(UITableView *)tableView sectionForSectionIndexTitle:(NSString *)title atIndex:(NSInteger)index
{
    //    [tableView setContentOffset:CGPointMake(0,41*(count-1)-20) animated:YES];
    return 5;
    
}


````


##  ios6 以后的新用法


旧的方法

````objc

static NSString *ID = @"cell";
UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID];
if (cell == nil) {
    cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:ID]; 
}

````

新方法可以这样用

````objc
static NSString *ID = @"cell";
[self.tableView registerClass:[MyCell class] forCellReuseIdentifier:ID];
UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:ID forIndexPath:indexPath];

````

区别在于之前的写法取出重用cell的时候可能是空的
而后来的写法如果取出空的那就自动创建一个新的 register就是告诉它创建个什么样的

