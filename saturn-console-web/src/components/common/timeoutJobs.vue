<template>
    <FilterPageList :data="timeoutJobsList" :total="total" :order-by="orderBy" :filters="filters">
        <template slot-scope="scope">
            <el-form :inline="true" class="table-filter">
                <input type="text" v-show="false"/>
                <el-form-item label="">
                    <el-input placeholder="请输入作业名" v-model="filters.jobName.value" @keyup.enter.native="scope.search"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" icon="el-icon-search" @click="scope.search">查询</el-button>
                </el-form-item>
            </el-form>
            <div class="page-table">
                <div class="page-table-header">
                    <div class="page-table-header-title">
                        <i class="fa fa-list"></i>
                        {{ type === 'disabledTimeout' ? '禁用' : '运行' }}超时（告警）的作业{{ type === 'disabledTimeout' ? '' : '分片' }}
                        <el-button type="text" @click="refreshList"><i class="fa fa-refresh"></i></el-button>
                    </div>
                </div>
                <el-table stripe border @sort-change="scope.onSortChange" :data="scope.pageData" style="width: 100%">
                    <el-table-column prop="jobName" label="作业名" sortable>
                        <template slot-scope="scope">
                            <router-link tag="a" :to="{ name: 'job_setting', params: { domain: scope.row.domainName, jobName: scope.row.jobName } }">
                                <el-button type="text">
                                    {{scope.row.jobName}}
                                </el-button>
                            </router-link>
                        </template>
                    </el-table-column>
                    <el-table-column prop="domainName" label="所属域" sortable>
                        <template slot-scope="scope">
                            <router-link tag="a" :to="{ name: 'job_overview', params: { domain: scope.row.domainName } }">
                                <el-button type="text">
                                    {{scope.row.domainName}}
                                </el-button>
                            </router-link>
                        </template>
                    </el-table-column>
                    <el-table-column prop="degree" label="域等级" width="110px" sortable>
                        <template slot-scope="scope">
                            {{$map.degreeMap[scope.row.degree]}}
                        </template>
                    </el-table-column>
                    <el-table-column prop="jobDegree" label="作业等级" width="110px" sortable>
                        <template slot-scope="scope">
                            {{$map.degreeMap[scope.row.jobDegree]}}
                        </template>
                    </el-table-column>
                    <el-table-column v-if="type !== 'disabledTimeout'" prop="timeout4AlarmSeconds" label="超时秒数" width="110px"></el-table-column>
                    <el-table-column v-if="type !== 'disabledTimeout'" prop="timeoutItems" label="超时分片"></el-table-column>
                    <el-table-column v-if="type === 'disabledTimeout'" prop="disableTimeStr" label="禁用时间" width="160px"></el-table-column>
                    <el-table-column v-if="type === 'disabledTimeout'" prop="disableTimeoutSeconds" label="设置超时秒数" width="110px"></el-table-column>
                    <el-table-column label="操作" width="100px" align="center">
                        <template slot-scope="scope">
                            <el-button v-if="$common.hasPerm('alarmCenter:setTimeout4AlarmJobRead', scope.row.domainName)" size="small" type="primary" @click="handleRead(scope.row)" :disabled="scope.row.read">不再告警</el-button>
                        </template>
                    </el-table-column>
                </el-table>
            </div>
        </template>
    </FilterPageList>
</template>
<script>
export default {
  props: ['timeoutJobsList', 'type'],
  data() {
    return {
      filters: {
        jobName: { value: '' },
      },
      orderBy: 'jobName',
      total: this.timeoutJobsList.length,
    };
  },
  methods: {
    refreshList() {
      this.$emit('refresh-list');
    },
    handleRead(row) {
      const params = {
        uuid: row.uuid,
        domainName: row.domainName,
      };
      this.$emit('no-alarm', params);
    },
  },
};
</script>
