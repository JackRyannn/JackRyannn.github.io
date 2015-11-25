---
author: JackRyannn
comments: true
date: 2015-11-25 16:15:00 -0700
layout: post
title: RecyclerView的使用
tags:
- Android

---
  
RecyclerView是2015年谷歌大会上发布的，介绍里第一句是这样说的：` A flexible view for providing a limited window into a large data set.`在有限的窗口内展现大量数据的灵活的视图。用它可以代替listview和gridview，更重要的是它是高度的解耦，在使用的时候就能够体会到了。  
  
简单写的例子，在注释中有对它的解释和思考。  
  
		public class MainActivity extends AppCompatActivity {
	//    必要的三部分，相比于listView多了一个layoutManager，layoutManager是一个抽象类
	//    用来测量和放置item，也可以制定item的回收规则，在这里，我们直接用的他的子类LinearLayoutManager
	    private RecyclerView recyclerView;
	    private RecyclerView.Adapter adapter;
	    private RecyclerView.LayoutManager layoutManager;
	//    数据list
	    private List<String> list;
	
	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        requestWindowFeature(Window.FEATURE_NO_TITLE);
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.content_main);
	        //初始化数据
	        initData();
	        recyclerView = (RecyclerView) findViewById(R.id.listView);
	        layoutManager = new LinearLayoutManager(this);
	//        adpater需要重写三个方法，必须重新创建ViewHolder
	        adapter = new RecyclerView.Adapter() {
	            @Override
	            public RecyclerView.ViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
	                MyViewHolder viewHolder = new MyViewHolder(LayoutInflater.from(MainActivity.this).inflate(R.layout.item_listview, parent, false));
	                return viewHolder;
	            }
	//用来更新item里的内容，不要用这里的position来做别的事情，比如onClick事件，你可以用getAdapterPosition来获取更新后的position
	            @Override
	            public void onBindViewHolder(RecyclerView.ViewHolder holder, int position) {
	                ((MyViewHolder) holder).textView.setText(list.get(position));
	            }
	
	            @Override
	            public int getItemCount() {
	                return list.size();
	            }
	//ViewHolder可以通过新建view填充或者从xml布局文件填充
	            class MyViewHolder extends RecyclerView.ViewHolder {
	                TextView textView;
	
	                public MyViewHolder(View itemView) {
	                    super(itemView);
	                    textView = (TextView) itemView.findViewById(R.id.textView);
	                }
	            }
	        };
	//        最后把adapter和layoutManager都设置好就可以了。
	        recyclerView.setAdapter(adapter);
	        recyclerView.setLayoutManager(layoutManager);
	    }
	
	    //初始化的方法
	    private void initData() {
	        list = new ArrayList<>();
	        for (int i = 0; i < 100; i++) {
	            list.add("id:" + i);
	        }
	    }
	}

