# StoneNeedle_kernel
base: linux 3.13 kernel (https://mirrors.edge.kernel.org/pub/linux/kernel/v3.x/)



Adding Ctags & Cscope
>make ctags  
>find "$PWD" -name "\*.c" -o -name "\*.h" | cscope -q -R -b




## unsigned int 형으로 buffer_head->ext4_type_for_stoneneedle 랑 bio->ext4_type_for_stoneneedle 라는 필드 파놓음  
0 : undefined  
1 : superblock  [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/1ee82346958eb81c3f16a5c5bc866d25c15a4cb4), [Code](https://github.com/ghdud4006/StoneNeedle_kernel/commit/e38b5aa7e66d32e354f4b95a5cb3961fe04b4641).  
2 : group descriptor - ext4_fill_super(), [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/63201855d557eeeeb517d77cb8af3356cc2a74e1), [Code](https://github.com/ghdud4006/StoneNeedle_kernel/commit/0116d24a361fad7b0e443bc5a07510bf20b1ea1d).   
3 : block bitmap  [Code](https://github.com/ghdud4006/StoneNeedle_kernel/commit/a509d7b2b2f54de477d54c820ff70ac1dd67bc99).  
4 : inode bitmap  [Code](https://github.com/ghdud4006/StoneNeedle_kernel/commit/681e51e636ea4414a68501d0af2bae63558d5a3b).  
5 : regular file inode  [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/c17e93bb1fe02a25e0f3ee66264d3349a9ad8900).  
6 : directory inode  
	create - ext4_add_entry(), [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/5b195b7d0e7adf2fc66c35eb77668c9c9aa7a336).  
	mkdir - ext4_append(), [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/da4d07ea899a574496dbb9af0be3a76ecd6b1bc0).  
	rename [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/9dd949242784af2f2f0d4ad1fe1a74b74713d003).  
	[Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/40382d2a6a4ec15897c6b185da5ef93e0148f9a1).  
7 : regular file data block [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/908ae75dcfbb3f0a6047739f4a41e36a9a814778).  
8 : directory data block  
9 : journal data  [Code](https://github.com/ghdud4006/StoneNeedle_kernel/commit/1df8117167be910fa8e952d6aba9b4071818bbed).


## bh의 필드에 type 남기는 법 (예시)

```c
	/* journal data 일 경우 */
	bh->ext4_type_for_stoneneedle = 9; 
```



## 아래 코드로 StoneNeedle 모듈이 bio 로부터 type 받으니 참고  

```c
  	/* superblock */
  	if (bio->ext4_type_for_stoneneedle == 1)
		calc_bucket_account(io_data->write_ext4_sb_per_chunk, bio,
			    io_data->bucket_size);
	/* group descriptor */
	else if (bio->ext4_type_for_stoneneedle == 2) 
		calc_bucket_account(io_data->write_ext4_grdesc_per_chunk, bio,
			    io_data->bucket_size);	
	/* block bitmap */
	else if (bio->ext4_type_for_stoneneedle == 3) 
		calc_bucket_account(io_data->write_ext4_bbmap_per_chunk, bio,
			    io_data->bucket_size);
	/* inode bitmap */
	else if (bio->ext4_type_for_stoneneedle == 4) 
		calc_bucket_account(io_data->write_ext4_ibmap_per_chunk, bio,
			    io_data->bucket_size);
	/* regular file inode */
	else if (bio->ext4_type_for_stoneneedle == 5) 
		calc_bucket_account(io_data->write_ext4_rinode_per_chunk, bio,
			    io_data->bucket_size);
	/* directory inode */
	else if (bio->ext4_type_for_stoneneedle == 6) 
		calc_bucket_account(io_data->write_ext4_dinode_per_chunk, bio,
			    io_data->bucket_size);
	/* regular file data block */
	else if (bio->ext4_type_for_stoneneedle == 7) 
		calc_bucket_account(io_data->write_ext4_rblock_per_chunk, bio,
			    io_data->bucket_size);
	/* directory data block */
	else if (bio->ext4_type_for_stoneneedle == 8) 
		calc_bucket_account(io_data->write_ext4_dblock_per_chunk, bio,
			    io_data->bucket_size);
	/* journal data */
	else if (bio->ext4_type_for_stoneneedle == 9) 
		calc_bucket_account(io_data->write_ext4_journal_per_chunk, bio,
			    io_data->bucket_size);
	/* undefined */
	else  
		return 0;
```
