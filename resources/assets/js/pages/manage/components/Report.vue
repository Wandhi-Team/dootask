<template>
    <div class="report">
        <Tabs v-model="reportTabs">
            <TabPane :label="$L('我的汇报')" name="my">
                <ReportMy ref="report" v-if="reportTabs === 'my'" @on-view="onView" @on-edit="onEditReport"></ReportMy>
            </TabPane>
            <TabPane :label="tabRebder(reportUnreadNumber)" name="receive">
                <ReportReceive v-if="reportTabs === 'receive'" @on-view="onView"></ReportReceive>
            </TabPane>
        </Tabs>
        <DrawerOverlay
            v-model="showDetailDrawer"
            placement="right"
            :size="950"
            transfer>
            <ReportDetail :data="detailData"/>
        </DrawerOverlay>
        <DrawerOverlay
            v-model="showEditDrawer"
            placement="right"
            :size="1000"
            transfer>
            <ReportEdit :id="reportId" @saveSuccess="saveSuccess"/>
        </DrawerOverlay>
    </div>
</template>

<script>
import ReportEdit from "./ReportEdit"
import ReportMy from "./ReportMy"
import ReportReceive from "./ReportReceive"
import ReportDetail from "./ReportDetail"
import DrawerOverlay from "../../../components/DrawerOverlay";

export default {
    name: "Report",
    components: {
        DrawerOverlay,
        ReportEdit, ReportMy, ReportReceive, ReportDetail
    },

    props: {
        reportUnreadNumber: {
            type: Number,
            default: 0
        },
        reportType: {
            default: "my"
        }
    },

    data() {
        return {
            reportTabs: "my",
            showDetailDrawer: false,
            showEditDrawer: false,
            detailData: {},
            reportId: 0
        }
    },

    mounted() {
        this.reportTabs = this.reportType;
    },

    methods: {
        tabRebder(num) {
            return h => {
                if (num > 0) {
                    return h('div', [
                        h('span', {class: 'navbar-item-content'}, this.$L('收到的汇报')),
                        h('Badge', {
                            class: 'manage-box-report',
                            props: {
                                count: num
                            }
                        }),
                    ])
                } else {
                    return h('div', [
                        h('span', {class: 'navbar-item-content'}, this.$L('收到的汇报')),
                    ])
                }
            }
        },

        onView(row) {
            this.showDetailDrawer = true;
            this.detailData = row;
            this.$emit("on-read");
        },

        onEditReport(id) {
            this.reportId = id;
            this.showEditDrawer = true;
        },

        saveSuccess() {
            this.reportId = 0;
            this.reportTabs = "my";
            this.showEditDrawer = false;
            this.$refs.report && this.$refs.report.getLists();
        }
    }
}
</script>

<style scoped>

</style>
