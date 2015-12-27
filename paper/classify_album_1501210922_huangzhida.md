## 各类图片按时刻、旅行、故事归类的研究与实现(1501210922 黄志达)

### 姓名：黄志达
### 学号：1501210922

#### 研究背景：由于系统自带的相册除了按时间归集之外，就没有其它类型的归集；但用户也想看到其他类型的归集，如旅行相册或故事相册等，或者是使用机器学习来识别图片是属于什么类型的相片。将图片归集成相册，会涉及到Android的sqlite数据库表设计和操作，网络请求模块，JNI调用C++库接口，归集算法的设计与分析


###  1.sqlite数据库设计
#### 主要四个表设计：
album_info 相册信息表

| 名称 | 类型 | 描述
| -- | -- |
| album_id | string    | pimary key |
| album_name | string  | 相册名称|
| album_status | int    | 相册状态 |
| album_type | int |  相册类型
| album_address | string | 相册地址描述
| album_time_str | string | 相册时间描述
| start_time | string |  相册开始时间
| end_time | string |    相册结束时间
| album_desc | string |  相册描述

photo_info 相片信息表

| 名称 | 类型 | 描述
| -- | -- | -- |
| photo_id | string | primary key |
| photo_url | string | 相片存储路径 |
| create_time | long | 相片拍摄时间 |
| photo_desc | string | 相片描述 |
| longitude | string | 经度 |
| latitude | string | 纬度 |
| country | string | 国家 |
| province | string | 省 |
| city | string | 城市 |
| district | string | 区县 |
| street | string | 街道 |
| spot | string | 附近景点 |

album_pic_ref 相册与相片的关系表

| 名称 | 类型 |
| -- | -- |
| album_id | string |
| photo_id | string |

pic_album_ref 相片属于哪个相册的关系表

| 名称 | 类型 |
| -- | -- |
| photo_id | string |
| album_id | string |


####  sqlite数据库操作：
创建数据库：

```@Override
	public void onCreate(SQLiteDatabase sqlitedatabase) {


		String albumTable = " create table album_info ( " +
				"ID INTEGER PRIMARY KEY  AUTOINCREMENT," +
				"unique_id text, " +
				"name text," +
				"type int," +
				"status int," +
				"collect_status int," +
				"template_id int," +
				"start_time BIGINT," +
				"end_time BIGINT," +
				"pic_cnt int," +
				"time_str text ," +
				"address text," +
				"create_time BIGINT," +
				"modify_time BIGINT" +
				" )";
		
		String picInfoTable = " create table pic_info ( " +
				"ID INTEGER PRIMARY KEY  AUTOINCREMENT," +
				"path text ," +
				"thumb_path text ," +
				"image_id bigint ," +
				"image_time bigint ,"+
				"file_create_time bigint ,"+
				"width int ," +
				"height int ," +
				"lat double , " +
				"lon double , " +
				"ifGetLoc int , " +
				"country text," +
				"province text," +
				"city text," +
				"district text," +
				"street text " +
				" )";

		String picAlbumRefTable = " create table pic_album_ref ( " +
				"ID INTEGER PRIMARY KEY  AUTOINCREMENT," +
				"image_id bigint ," +
				"album_id text " +
				" )";

		sqlitedatabase.execSQL(albumTable);
		sqlitedatabase.execSQL(picInfoTable);
		sqlitedatabase.execSQL(picAlbumRefTable);

	}```

分页拉取相册里所有的相片

```@Override
	public List<PicInfoDto> getAlbumPics(String albumId ,int index ,int pageCnt) {
		long start = System.currentTimeMillis();

		String[] args = {albumId};

		String sql = "select " +
				" DISTINCT pic.image_id image_id ," +
				" pic.path path," +
				" image_time ,"+
				" file_create_time ,"+
				" thumb_path ," +
				" width ," +
				" height ," +
				" ifGetLoc , " +
				" lat ,  lon , " +
				" country , province , city ,  district , street " +
				" from pic_album_ref ref join pic_info pic on ref.image_id = pic.image_id " +
				" where ref.album_id = ? ";

		if( pageCnt != 0){
			sql = sql + " limit "+index+", "+pageCnt;
		}else {
			sql = sql + "limit 10 ";
		}

		Cursor cur = db.rawQuery(sql,args);
		
		List<PicInfoDto> rs = DbUtil.genObjList(cur, new PicInfoDto());
		cur.close();
		Utils.printCost("getAlbumPics ", start);
		LogUtil.i(TAG, "getAlbumPics...albumId="+albumId+", rs="+rs);
		return rs;
	}```


###  2.获取安卓系统的所有图片

    android系统有自带的多媒体数据库MediaStore.
    MediaStore包括了多媒体数据库的所有信息，
    包括音频，视频和图像，android对所有的多媒体数据库接口进行了封装；下面关于这些接口的用法：
    要想得到一个ContentResolver实例， 利用Activity或者Service的Context.
    
    ContentResolver mResolver = ctx.getContentResolver();
    
    ctx就是一个context, Activity.this就是那个Context，
    利用它就可以调用getContentResolver接口获取ContentResolver实例
    
    之后通过调用如下代码进行查询：
    
    Cursor cursor = mresolver.query(uri, prjs, selections, selectArgs, order);
    
    ContentResolver的query方法接受几个参数：
    
    uri: 代表要查询的数据库名称加上表的名称；如获取图片MediaStore.Images.Media.EXTERNAL_CONTENT_URI;
    prjs: 要从表中选择的列，用一个String数组来表示
    selections: 查询条件，相当于sql的where语句
    selectArgs: 
    Order: 说明查询结果按什么来排序
    
代码实现：
    
   　扫描图片的部分代码实现
 
	private List<PicInfoDto> getScanRs(long lastScanTime,int max_cnt)
	{
		LogUtil.i(TAG, "getScanRs... begin.");
		long start = System.currentTimeMillis();
		ContentResolver mContentResolver = ctx.getContentResolver();
		// image search
		Uri mImageUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI;
		String[] projection = {
				MediaStore.Images.Media.DATA,
				MediaStore.Images.Thumbnails.DATA,
				MediaStore.Images.Media._ID,
				MediaStore.Images.Media.DATE_MODIFIED,
				MediaStore.Images.Media.LONGITUDE,
				MediaStore.Images.Media.LATITUDE,
				MediaStore.Images.Media.WIDTH,
				MediaStore.Images.Media.HEIGHT
		};
		
		String selection ="("+MediaStore.Images.Media.MIME_TYPE + "=? or "
				+ MediaStore.Images.Media.MIME_TYPE + "=? ) and "+
				MediaStore.Images.Media.DATE_MODIFIED +"> ? and "+ MediaStore.Images.Media.SIZE +" > "+FileSize_min;; 
		String[] args = new String[] { "image/jpeg", "image/png" , lastScanTime+"" };

		Cursor mCursor = mContentResolver.query(mImageUri, projection,selection,
				args, MediaStore.Images.Media.DATE_MODIFIED +" asc ");


		List<PicInfoDto> rs = new ArrayList<PicInfoDto>();

		while (mCursor.moveToNext()) {
			String path = mCursor.getString(mCursor.getColumnIndex(MediaStore.Images.Media.DATA));

			long image_id = mCursor.getLong(mCursor.getColumnIndex(MediaStore.Images.Media._ID));
			long addTimeS = mCursor.getLong(mCursor.getColumnIndex(MediaStore.Images.Media.DATE_MODIFIED)); // second
			long addTimeMs = addTimeS * 1000;

			float lon = mCursor.getFloat(mCursor.getColumnIndex(MediaStore.Images.Media.LONGITUDE));
			float lat = mCursor.getFloat(mCursor.getColumnIndex(MediaStore.Images.Media.LATITUDE));

			int width = mCursor.getInt(mCursor.getColumnIndex(MediaStore.Images.Media.WIDTH));
			int height = mCursor.getInt(mCursor.getColumnIndex(MediaStore.Images.Media.HEIGHT));

			PicInfoDto tmpPic = new PicInfoDto();
			tmpPic.setPath(path);
			tmpPic.setImage_id(image_id);
			tmpPic.setFileCreateTime(addTimeMs);
			tmpPic.setLon(lon);
			tmpPic.setLat(lat);
			tmpPic.setWidth(width);
			tmpPic.setHeight(height);

			rs.add(tmpPic);
			if(rs.size() >= max_cnt){
				break;
			}
		}
		mCursor.close();
		LogUtil.i(TAG, "getScanRs... end.rs.size ="+rs.size()+", rs="+rs);
		start =	Utils.printCost("query_image ", start);
		
		for (PicInfoDto pic : rs) {
			long tmp = getImageDateMs(pic.getPath(),pic.getFileCreateTime());
			pic.setImageTime(tmp);
		}
		Utils.printCost("get_create_time", start);
		return rs;
	}


###  3.调用网络模块获取图片地址

    
    地址服务器：首先客户端发送请求到服务器，如果服务器已经缓存了gps坐标的相应的地理信息，
    直接返回给客户端，如果没有缓存，国内的gps坐标调用高德的API,　国外的地址则通过国外的
    服务器访问google地图的API来获取地址，服务器都会缓存已经获取过的地址，存储在mysql, 
    缓存在redis数据库；
    
    客户端调用批量的提交post gps坐标到服务器API，服务端返回使用JSON的图片地址；
    
    首先生成一个JSONArray对象，构建批量的GPS坐标信息
    JSONArray jarr = new JSONArray();
	for (PicInfoDto pic : picList) {
		JSONObject jobj = new JSONObject();
		jobj.put("id", pic.getImage_id());
		jobj.put("lng", pic.getLon());
		jobj.put("lat", pic.getLat());
		jarr.add(jobj);
	}
    final String data = "data="+jarr.toJSONString();
    
    
    构建请求，并放在网络请求队列，然后线程不断地从队列里获取数据发送请求
    StringRequest strReq = new StringRequest(Method.POST, url, resOkL ,resErrL)
    	@Override
    	public byte[] getBody() throws AuthFailureError {
    		byte[] rsB = null;
    		try {
    			LogUtil.i(TAG, "param="+data);
    			rsB = data.getBytes("utf-8");
    		} catch (Exception e) {
    			LogUtil.e(TAG, e);
    		}
    		return rsB;
    	}
    };
    netActionsHandler.addToRequestQueue(strReq, "");
    
    注册返回地址信息的监听处理：
    Response.Listener<String> resOkL = new Response.Listener<String>() {
		@Override
		public void onResponse(String response) {
			LogUtil.d(TAG, "[onResponse]..."+response.toString());
			JSONObject jobj = JSON.parseObject(response);
			int rs_code = jobj.getInteger("code");
			if(rs_code == 200){
				List<lation> rs = JSON.parseArray(jobj.getString("data"),lation.class);
				HashMap<String, lation> tmpMap = new HashMap<String, GetLocationAct.lation>();
				for (lation la : rs) {
					tmpMap.put(la.id, la);
				}
				getLocation_cnt += picList.size();
				for (PicInfoDto pic : picList) {
					lation targetLa = tmpMap.get(String.valueOf(pic.getImage_id()));
					pic.setIfGetLoc(1);
					pic.setCountry(targetLa.country);
					pic.setProvince(targetLa.province);
					pic.setCity(targetLa.city);
					pic.setStreet(targetLa.street);
					pic.setDistrict(targetLa.district);
				}
				dbActionHandler.updatePicInfo(picList);
			}
			Utils.printCost("getLocationBat picList="+picList.size(), start);
			checkBat();
		}
	};
    
    
    
###  4.Android调用C++库

    Java Native Interface java本地接口支持将Java 代码与使用其他编程语言编写的代码相集成。
    
    链接Java对象到C++对象

　　链接C++对象到Java对象
　　
　　```(JNIEnv * jEnv, jobject jObj)```
　　　
　　JEnv 指向的是JNI的运行环境，jobj是本地任务对象的引用

    ```
    jclass jClass;
    jmethodID jMethID;
   
    jClass = jEnv->GetObjectClass(jObj);
    jMethID = jEnv->GetMethodID(jClass, "levelChanged", "(I)V");
    jEnv->CallVoidMethod(jObject, jMethID, level);```
    
    
    

    
    
###  5.底层C++归集算法

####  a. 时刻相册归集
    
    上层把扫描好的相片逐步传入底层，底层使用日期作为key，album_info作为value的map结构
    把相片按照日期进行归集，同时把一些连续天数零碎的相片合并成一个相册；然后通过回调的
    方式返回到上层
    代码实现：
    
    void classify_photo_to_album::classify_photo_into_no_addr_moment() {
	uint32_t  v_len = 0;
	bool	  same_moment_album = false;

	map<string, album_info>::iterator it;
	map<string, album_info>  *p_write_album_map = NULL;
	map<string, album_info>   total_album_map;
	c_photo_config *c_conf = singleton_base<c_photo_config>::get_instance();

	//合并之前未完成的相册
	if (c_conf->vec_album_unfinish.size() > 0) {
		combine_new_and_unfinish_album(total_album_map, ENUM_ALBUM_TYPE_MOMENT);
	}

	if (total_album_map.size() > 0) {
		p_write_album_map = &total_album_map;
	} else {
		p_write_album_map = &c_conf->write_album_map;
	}

	//对每天的相册的照片进行排序
	sort_album_map_photo(p_write_album_map);

	album_info a_info;

	//循环获取初步按天数分类的相册
	for (it = p_write_album_map->begin(); it != p_write_album_map->end(); it++) {

		//判断当天相册是否少于要求的最少相片个数
		if (it->second.vec_photo_all.size() > MIN_MOMENT_ALBUM_NUM) {
			//可能需要进一步根据地点或具体的时间进一步细化
			if (a_info.vec_photo_all.size() > 0) {
				it--;
			} else {
				a_info = it->second;
			}
			same_moment_album = true;
		} else {
			//合并少于要求最少相片个数的相册
			if (a_info.vec_photo_all.size() > 0) {
				v_len = it->second.vec_photo_all.size();
				for (uint32_t i = 0; i < v_len; i++) {
					a_info.vec_photo_all.push_back(it->second.vec_photo_all[i]);
				}
			} else {
				a_info = it->second;
			}
		}

		it++;
		if (same_moment_album || it == p_write_album_map->end())  {

			if (a_info.vec_photo_all.size() > 0) {

				if (same_moment_album) {
					set_album_other_info(a_info, ENUM_ALBUM_TYPE_MOMENT, ENUM_ALBUM_STATUS_NO_FINISH);
				} else {
					set_album_other_info(a_info, ENUM_ALBUM_TYPE_MOMENT, ENUM_ALBUM_STATUS_NO_FINISH);
				}

				dump_album_info(a_info);

			   //单个相册已经构建完成， 调用回调函数返回到上层
				single_album_callback(a_info);

				a_info.vec_photo_all.clear();
				a_info.album_id = "";
			}
			same_moment_album = false;
		}
		it--;
	}

	//删除上次未finish的相册
	if (c_conf->vec_album_unfinish.size() > 0) {
		del_prior_unfinish_album(ENUM_ALBUM_TYPE_MOMENT);
	}
}
    
####  b. 旅行相册归集
    
    根据图片的exif信息获取到GPS信息，然后通过GSP的坐标得到图片的拍摄地址，算法首先是算出
    每张图片的地址分布和天数分布，从而根据地址的分布和天数初步算出常用居住地(家乡地址), 
    之后根据家乡地址算出旅行的回路，形成一个相册，如果不能形成回路，即根据超出了最大的
    旅行时间限制则形成一个相册
    代码实现：
    
    void classify_photo_to_album::classify_photo_into_travel() {
	uint32_t  rd_idx = 0;
	uint32_t  v_len = 0;
	bool	  same_travel_album = false;
	string    city = "";
	string 	  first_home_city = "";
	string 	  second_home_city = "";

	album_info a_info;
	map<string, album_info>::iterator it;
	map<string, album_info>  *p_write_album_map = NULL;
	map<string, album_info>   total_album_map;
	c_photo_config *c_conf = singleton_base<c_photo_config>::get_instance();

	find_home_city(&c_conf->write_album_map);

	if (c_conf->vec_album_unfinish.size() > 0) {
		combine_new_and_unfinish_album(total_album_map, ENUM_ALBUM_TYPE_TRAVEL);
	}

	if (total_album_map.size() > 0) {
		//还没有确定家乡地址的接口
		p_write_album_map = &total_album_map;
		sort_album_map_photo(p_write_album_map);
	} else {
		p_write_album_map = &c_conf->write_album_map;
	}

	if (c_conf->vec_home_city.size() > 0) {

		first_home_city = c_conf->vec_home_city[0];
		if (c_conf->vec_home_city.size() > 1) {
			second_home_city = c_conf->vec_home_city[1];
		}

		WRITE_TRACE("first_home_city: %s  second_home_city: %s", first_home_city.c_str(), second_home_city.c_str());

		//循环获取初步按天数分类的相册
		for (it = p_write_album_map->begin(); it != p_write_album_map->end(); it++) {

			v_len = it->second.vec_photo_all.size();
			uint32_t i = rd_idx;

			//循环获取每个album里的vector相片信息
			for(; i < v_len; i++) {
				city = it->second.vec_photo_all[i].photo_address.city;
				if (!city.empty())
				{
					//判断是否能够形成旅行回路
					if (city.compare(first_home_city) == 0 || (!second_home_city.empty() && city.compare(second_home_city) == 0)) {
						if (a_info.vec_photo_all.size() > MIN_TRAVEL_ALBUM_NUM) {
							rd_idx = i + 1;
							if (rd_idx != v_len) {
								it--;
							}
							//WRITE_TRACE("city %d : %s", i, it->second.vec_photo_all[i].photo_address.city.c_str());
							same_travel_album = true;
							break;
						} else {
							if (a_info.vec_photo_all.size() > 0) {
								a_info.vec_photo_all.clear();
							}
						}
					} else {
						if (i == 0) {
							//检查是否超过最大的旅行时间来分辨和由当前构建相册的个数来决定
							if (a_info.vec_photo_all.size() > 0) {
								uint32_t intvl_days = (it->second.vec_photo_all[i].create_time -
														a_info.vec_photo_all.back().create_time)/3600000/24;
								if (intvl_days > MAX_TRAVEL_DAY_LONG) {
									//超出最大的旅行时间
									if (a_info.vec_photo_all.size() > MIN_TRAVEL_ALBUM_NUM) {
										it--;
										rd_idx = i;
										//WRITE_TRACE("intvl_days: %d", intvl_days);
										same_travel_album = true;
										break;
									} else {
										a_info.vec_photo_all.clear();
									}
								}
							}
						}
						a_info.vec_photo_all.push_back(it->second.vec_photo_all[i]);
					}
				}
			}
			//当前的album相册信息已经获取完成
			if (i == v_len) {
				rd_idx =  0;
			}

			it++;
			if (same_travel_album || it == p_write_album_map->end()) {
				if (a_info.vec_photo_all.size() > 0) {

					if (same_travel_album) {
						set_album_other_info(a_info, ENUM_ALBUM_TYPE_TRAVEL, ENUM_ALBUM_STATUS_FINISHED);
					} else {
						set_album_other_info(a_info, ENUM_ALBUM_TYPE_TRAVEL, ENUM_ALBUM_STATUS_NO_FINISH);
					}
					set_album_address(a_info);
					dump_album_info(a_info);

					//构建单个旅行相册完成，回调函数返回上层
					single_album_callback(a_info);
					same_travel_album = false;
				}
				//tmp_city = "";
				a_info.album_id = "";
				a_info.vec_photo_all.clear();
			}
			it--;
		}

		//删除上次未finish的相册
		if (c_conf->vec_album_unfinish.size() > 0) {
			del_prior_unfinish_album(ENUM_ALBUM_TYPE_TRAVEL);
		}
	}
}
    
    
####  c. 故事相册归集
    
    主要是找出一些聚会，或者会议，或者在公园玩的一些故事相册；简单来说，就是找出相片时间比较集中的时间点形成一个故事相册；这可以使用一维的k-means算法实现，找出比较聚集的相片
    然后形成一个相册；也可以使用时间滑动窗口的方式找出拍摄频度比较高的时间段形成相册，
    同时为了提高故事的准确性，加上获取到的地点和图片GPS坐标算出图片距离进行综合考虑；
    代码初步实现：
    
    
    void classify_photo_to_album::classify_photo_into_story() {
	uint32_t  v_len = 0;
	uint32_t  q_len = 0;
	//uint32_t  days = 0;
	//uint32_t  all_photo_num = 0;
	string 	  city = "";
	bool	  same_event_album = false;
	string 	  first_home_city = "";
	string 	  second_home_city = "";


	album_info				a_info;
	queue<photo_info>		story_photo_queue;
	map<string, album_info>::iterator it, it_next;
	map<string, album_info>  *p_write_album_map = NULL;
	map<string, album_info>   total_album_map;
	c_photo_config *c_conf = singleton_base<c_photo_config>::get_instance();

	if (c_conf->vec_album_unfinish.size() > 0) {
		combine_new_and_unfinish_album(total_album_map, ENUM_ALBUM_TYPE_STORY);
	}

	if (total_album_map.size() > 0) {
		p_write_album_map = &total_album_map;
		sort_album_map_photo(p_write_album_map);
	} else {
		p_write_album_map = &c_conf->write_album_map;
	}

	//动态调整阀值
	/*days = p_write_album_map->size();
	for (it = p_write_album_map->begin(); it != p_write_album_map->end(); it++) {
		all_photo_num +=  it->second.vec_photo_all.size();
	}
	MIN_STORY_ALBUM_NUM = all_photo_num / days;*/
	
	uint32_t MIN_STORY_ALBUM_NUM = 15;
	
	pre_proc_classify_story(p_write_album_map, MIN_STORY_ALBUM_NUM);

	if (c_conf->vec_home_city.size() > 0) {

		first_home_city = c_conf->vec_home_city[0];
		if (c_conf->vec_home_city.size() > 1) {
			second_home_city = c_conf->vec_home_city[1];
		}

		//循环获取初步按天数分类的相册
		for (it = p_write_album_map->begin(); it != p_write_album_map->end(); it++) {

			v_len = it->second.vec_photo_all.size();

			//循环获取每个album里的vector相片信息
			for(uint32_t i = 0; i < v_len; i++) {
				//只计算本地的故事
				city = it->second.vec_photo_all[i].photo_address.city;
				if (city.compare(first_home_city) == 0 || (!second_home_city.empty() && city.compare(second_home_city) == 0))
				{
					uint64_t cur_create_time = it->second.vec_photo_all[i].create_time;
					story_photo_queue.push(it->second.vec_photo_all[i]);
					if (story_photo_queue.size() >= MIN_STORY_ALBUM_NUM) {
						for(; ;) {
							if (cur_create_time - story_photo_queue.front().create_time > STORY_ALBUM_WINDOW_SIZE) {
								if (same_event_album) {
									a_info.vec_photo_all.push_back(story_photo_queue.front());
								}
								story_photo_queue.pop();
							} else {
								break;
							}
						}
						//判断是否超出了滑动窗口的相片阀值
						if (story_photo_queue.size() >= MIN_STORY_ALBUM_NUM) {
							same_event_album = true;
						}
					} else {
						if (same_event_album) {
							q_len = story_photo_queue.size() - 2;
							for (uint32_t j = 0; j < q_len; j++) {
								a_info.vec_photo_all.push_back(story_photo_queue.front());
								story_photo_queue.pop();
							}
							same_event_album = false;

							set_album_address(a_info);
							set_album_other_info(a_info, ENUM_ALBUM_TYPE_STORY, ENUM_ALBUM_STATUS_FINISHED);
							dump_album_info(a_info);

							//构建单个故事相册完成，回调函数返回上层
							single_album_callback(a_info);
							a_info.album_id = "";
							a_info.vec_photo_all.clear();
						}
					}
				}
			}
		}

		if (story_photo_queue.size() > 0) {
			q_len = story_photo_queue.size();
			for (uint32_t j = 0; j < q_len; j++) {
				a_info.vec_photo_all.push_back(story_photo_queue.front());
				story_photo_queue.pop();
			}
			set_album_other_info(a_info, ENUM_ALBUM_TYPE_STORY, ENUM_ALBUM_STATUS_NO_FINISH);
			dump_album_info(a_info);

			//构建单个故事相册完成，回调函数返回上层
			single_album_callback(a_info);
		}
	} else {
		//还没有常用居住地，形成未完成的故事集合

		//循环获取初步按天数分类的相册
		for (it = p_write_album_map->begin(); it != p_write_album_map->end(); it++) {
			v_len = it->second.vec_photo_all.size();
			//循环获取每个album里的vector相片信息
			for(uint32_t i = 0; i < v_len; i++) {
				//只计算本地的故事
				city = it->second.vec_photo_all[i].photo_address.city;
				if (!city.empty()) {
					a_info.vec_photo_all.push_back(it->second.vec_photo_all[i]);
				}
			}
		}
		set_album_other_info(a_info, ENUM_ALBUM_TYPE_STORY, ENUM_ALBUM_STATUS_NO_FINISH);
		dump_album_info(a_info);

		//构建单个故事相册完成，回调函数返回上层
		single_album_callback(a_info);
	}

	//删除上次未finish的相册
	if (c_conf->vec_album_unfinish.size() > 0) {
		del_prior_unfinish_album(ENUM_ALBUM_TYPE_STORY);
	}

}
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    