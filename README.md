# StoneNeedle_kernel
base: linux 3.13 kernel (https://mirrors.edge.kernel.org/pub/linux/kernel/v3.x/)



Adding Ctags & Cscope
>make ctags  
>find "$PWD" -name "\*.c" -o -name "\*.h" | cscope -q -R -b




## unsigned int 형으로 buffer_head->ext4_type_for_stoneneedle 랑 bio->ext4_type_for_stoneneedle 라는 필드 파놓음  

0 : superblock  
1 : group descriptor - ext4_fill_super(), [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/63201855d557eeeeb517d77cb8af3356cc2a74e1).  
2 : block bitmap  
3 : inode bitmap  
4 : regular file inode  
5 : directory inode  
	mkdir - ext4_append(), [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/da4d07ea899a574496dbb9af0be3a76ecd6b1bc0).  
	rename [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/9dd949242784af2f2f0d4ad1fe1a74b74713d003)
6 : regular file data block  
7 : directory data block  
8 : journal data  [Link](https://github.com/ghdud4006/StoneNeedle_kernel/commit/054b8a654d02d3de2f2106ec7458e2b843d2f0b9).

아래 코드로 StoneNeedle 모듈이 bio 로부터 type 받으니 참고  

```c
  /* superblock */
  	if (bio->ext4_type_for_stoneneedle == 0)
		calc_bucket_account(io_data->write_ext4_sb_per_chunk, bio,
			    io_data->bucket_size);
	/* group descriptor */
	else if (bio->ext4_type_for_stoneneedle == 1) 
		calc_bucket_account(io_data->write_ext4_grdesc_per_chunk, bio,
			    io_data->bucket_size);	
	/* block bitmap */
	else if (bio->ext4_type_for_stoneneedle == 2) 
		calc_bucket_account(io_data->write_ext4_bbmap_per_chunk, bio,
			    io_data->bucket_size);
	/* inode bitmap */
	else if (bio->ext4_type_for_stoneneedle == 3) 
		calc_bucket_account(io_data->write_ext4_ibmap_per_chunk, bio,
			    io_data->bucket_size);
	/* regular file inode */
	else if (bio->ext4_type_for_stoneneedle == 4) 
		calc_bucket_account(io_data->write_ext4_rinode_per_chunk, bio,
			    io_data->bucket_size);
	/* directory inode */
	else if (bio->ext4_type_for_stoneneedle == 5) 
		calc_bucket_account(io_data->write_ext4_dinode_per_chunk, bio,
			    io_data->bucket_size);
	/* regular file data block */
	else if (bio->ext4_type_for_stoneneedle == 6) 
		calc_bucket_account(io_data->write_ext4_rblock_per_chunk, bio,
			    io_data->bucket_size);
	/* directory data block */
	else if (bio->ext4_type_for_stoneneedle == 7) 
		calc_bucket_account(io_data->write_ext4_dblock_per_chunk, bio,
			    io_data->bucket_size);
	/* journal data */
	else if (bio->ext4_type_for_stoneneedle == 8) 
		calc_bucket_account(io_data->write_ext4_journal_per_chunk, bio,
			    io_data->bucket_size);
	/* undefined */
	else  
		return 0;
```
